# Telebot Creator — AI Agent Build Guide

How an AI agent builds and edits a Telegram bot on Telebot Creator (TBC) using the
**MCP tools** — safely, additively, and verifiably.

> **You do not need an API key.** If you can see tools beyond the four doc tools
> (`list_docs`, `search_docs`, `get_doc`, `get_full_docs`), you are already
> authenticated over OAuth and every call is scoped to the owner's account.
> **Never ask the user for a TBC API key, and never call the REST API for
> anything a tool already covers.**

---

## 0. The golden rules (read first)

1. **ALWAYS `get_command` before `save_command`.** The owner may have edited that
   command in the dashboard since you last read it. Fetch the live code, diff it
   against what you expect, and only then save. Saving blind silently destroys
   someone's live work — there is no undo.
2. **ONE `save_command` at a time.** Never push a batch. Save one command, then
   `test_command` it, then move to the next. `import_commands` is for the initial
   scaffold of a brand-new bot only — never for edits.
3. **Saves are ADDITIVE.** `save_command` upserts a single command and touches
   nothing else. Other commands are never affected.
4. **Deleting is nuclear.** `delete_command` removes one command permanently.
   `import_commands` with `remove_old_commands: true` wipes the whole bot first —
   never use it unless the user explicitly asks to rewrite the bot from scratch.
5. **TPY is not Python.** No `import`. No filesystem. A command is a flat script,
   so a top-level `return` is a `SyntaxError` (see §4).
6. **A save is not a pass.** Code that saves can still crash at runtime. It is not
   working until `test_command` has actually run it.

---

## 1. The workflow

Every change goes through this loop. Do not skip steps 2 or 5.

```
1. list_bots                -> find the target bot, confirm it with the user
2. get_command              -> read the CURRENT live code (never edit blind)
3. validate_tpy             -> catch `return`, `import`, syntax errors while drafting
4. save_command             -> ONE command
5. test_command             -> actually run it; read the output carefully
6. get_errors               -> recent runtime failures for this bot
```

Repeat 2→6 per command. Because saves are additive and single, a mistake is
always contained to one command.

### Reading `test_command` output

- **`DID NOT RUN`** — the update never reached the bot. This is **NOT a pass**.
  Usual causes: the bot is stopped (`start_bot`), the token was revoked, or the
  test user has never pressed Start on the bot.
- **`No runtime errors were recorded`** *after a successful run* — this is the
  only result that means it works.

### Multi-step flows

- `get_pending_wait` — confirm a `Bot.handleNextCommand` flow actually armed.
  Then call `test_command` again with the reply text to walk the next step.
- `clear_wait` — reset a flow that is stuck mid-conversation.
- For inline buttons, call `test_command` with `callback_data` set to that
  button's value.

---

## 2. Resolving the target bot

- If the user gives a **numeric bot id**, use it directly.
- If the user only **names** a bot, call `list_bots` and match on name/username.
  Prefer running bots, then **confirm the exact bot (id + name + @username) with
  the user** before changing anything. If several match, ask which.
- For a **new** bot, `create_bot` with a token from @BotFather. It is idempotent —
  re-importing the same token returns the existing bot id rather than duplicating.

---

## 3. Editing an existing command safely

```
get_command(botid, "/start")     # 1. read live code
                                 # 2. is it what you last wrote? if not, the owner
                                 #    edited it -- show them the diff and ASK
validate_tpy(new_code)           # 3. check before sending
save_command(botid, "/start")    # 4. one command
test_command(botid, "/start")    # 5. prove it runs
```

If the live code differs from what you expect, **stop and surface it**. Do not
silently overwrite. The owner's live edit always wins unless they say otherwise.

---

## 4. Writing TPY commands (the rules that matter)

- **No `import`.** TPY blocks it. Everything you need is a built-in (`bot`, `Bot`,
  `User`, `libs.*`, `HTTP`, `time`, `options`, `params`, `message`, `call`, …).
- **Command names** can be anything non-empty: `/start`, `Daily Bonus`, `menu`,
  `check_balance`, and special triggers `*` (catch-all), `!` (error handler),
  `@`, `~`, `.env`. All import fine.
- **NEVER use a bare `return`.** This is the single most common mistake.
  A TPY command is a **flat script compiled with `exec()`**, not a function, so
  `return` at the top level is a hard `SyntaxError` and the command will not run
  at all. To stop early, `raise ReturnCommand`:

  ```python
  # WRONG — SyntaxError: 'return' outside function. The whole command dies.
  if not User.getData("verified"):
      bot.sendMessage("Please verify first")
      return

  # RIGHT
  if not User.getData("verified"):
      bot.sendMessage("Please verify first")
      raise ReturnCommand
  ```

  `return` is only legal **inside a `def` you wrote yourself** in the command.
  Aliases `returncommand` and `returnCommand` also work.

- **Each command is a flat script** — no function wrapper around the whole thing
  (helper `def`s inside are fine).
- **No local files / disk.** TPY is sandboxed: send media by `file_id`, URL, or dict —
  never a local path.
- **Config pattern:** put shared config (APP_URL, ADMIN_IDS, etc.) in a `.env` command
  that's injected as globals, and keep hard-coded fallbacks in each command so it still
  runs if injection is unavailable.
- **Premium emoji** embed as `<tg-emoji emoji-id="...">😀</tg-emoji>` with
  `parse_mode="HTML"`; non-Premium users see the plain fallback. (See `PREMIUM_EMOJI_PALETTE.md`.)

### Platform limits that will SILENTLY break a bot

These are enforced by the runtime. Nothing crashes loudly, the behaviour just stops,
so design around them rather than discovering them in production.

| Limit | Value | What happens when you exceed it |
|---|---|---|
| **Chained `Bot.runCommand`** | **3 commands per user message** | The 4th call is refused. `/a` → `/b` → `/c` is fine; adding `/d` silently does nothing. |
| `Bot.runCommandAfter` schedules | 60 per minute, per user | Raises `ValueError`. |
| Outstanding scheduled tasks | 50,000 per user | Raises `ValueError`. |
| `Bot.runCommandAfter` delay | min **1 second**, max 366 days | Raises `ValueError`. You cannot schedule sub-second. |
| Commands per bot | **400** | Create is refused. |
| Code size per command | **256 KB** | Save is refused. |
| Bot data value (`Bot.saveData`) | **2 MB** | Save is refused with a 400. |

**The chaining limit is the one that bites hardest.** A long `runCommand` chain looks
correct in code and simply stops partway through. If you need more steps, do NOT chain:
use `Bot.handleNextCommand` (waits for user input) or `Bot.runCommandAfter` (a timer).
**Both start a fresh chain**, so neither is affected by the 3-command limit.

```python
# BAD: silently truncated at the 4th hop
Bot.runCommand("step2")   # step2 runs step3, step3 runs step4 -> step4 never fires

# GOOD: a timer resets the chain
Bot.runCommandAfter(1, "step2")
```

---

---

## 5. Looking things up

Use the doc tools, not HTTP:

- `get_full_docs` — the core reference bundle. Start here when building anything
  non-trivial; it also lists the extra pages you can pull on demand.
- `search_docs` — find where something is documented (single keywords work best).
- `get_doc` — one page by name.
- `list_docs` — everything available.

Key pages: `tpy-language-reference`, `tbc-libraries-libs`, `command-in-tpy`,
`special-commands`, `broadcast-function-in-tbc`.

---

## 6. Decision cheat-sheet

| Situation | What to do |
|---|---|
| Add a command | `save_command` (one). Nothing else is touched. |
| Edit a command | `get_command` first, then `save_command`, then `test_command`. |
| Add several commands | One `save_command` each, testing between. Never a batch. |
| Scaffold a brand-new bot | `import_commands` once, then switch to `save_command` for all edits. |
| Rename a command | Save under the new name, then `delete_command` the old one once the new one tests clean. |
| Remove one command | `delete_command`. Permanent — confirm with the user. |
| Full rewrite | `import_commands` + `remove_old_commands: true`. Destructive; explicit user request only. |
| Bot not responding | `get_bot` (is it running?), then `start_bot`, then `get_errors`. |

---

## 7. Quick start

```
list_bots                       # find + confirm the bot
get_full_docs                   # load the core TPY reference
validate_tpy  <code>            # draft, check
save_command  /start  <code>    # one command
test_command  /start            # prove it runs -- read the output
get_errors                      # anything failing?
```

Build one command at a time, read before you write, and test before you move on.
