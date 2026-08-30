# Special Commands: `@`, `~`, `!`, `*`

Telebot Creator reserves **four command names** that have special runtime behavior.
You never type them as a user — the platform runs them **automatically** at specific
points in a bot's lifecycle. Create them like any other command (a command whose name
is `@`, `~`, `!`, or `*`).

| Command | Role | Runs when | Can stop the command? |
|---|---|---|---|
| `~` | **Preface** | Prepended to *every* command | No |
| `@` | **Pre-processor / gate** | *Before* every command | Yes — `raise ReturnCommand` |
| `!` | **Error handler** | When a command *errors* | — |
| `*` | **Wildcard / fallback** | When *no* command matches | — |

---

## `~` — Preface (shared code on top of every command)

The code in your `~` command is **prepended to the code of every command** and runs in
the **same execution and namespace** as that command. Think of it as a shared header:
anything you define in `~` (variables, helper values) is available to the command that
runs after it.

- Runs **inline**, as part of the command — no extra latency.
- **Cannot** stop a command from running (it has no gate/return power).
- Order: `~` code first, then the command's code, as one program.

```python
# ~  (preface)
BRAND   = "Acme Bot"
support = "@acme_support"
# every command can now use BRAND and support directly
```

```python
# /help
bot.sendMessage(f"Welcome to {BRAND}. Need help? Message {support}.")
```

Use `~` for: constants, common greetings/footers, shared helper values, feature flags —
anything every command needs without repeating it.

---

## `@` — Pre-processor / gate (runs before every command)

The `@` command runs **before every command** — for text messages, callback queries,
and other updates. Use it to **validate or gate** requests before the real command runs:
force-subscribe / membership checks, onboarding funnels, blocking, per-user checks.

**Key power — skip the command with `raise ReturnCommand`:**

```python
# @  (pre-processor)
# Only let members of @mychannel use the bot; otherwise stop the command.
if not MembershipCheck(["@mychannel"], u):
    bot.sendMessage("Please join @mychannel first, then try again.")
    raise ReturnCommand   # <-- STOPS the command the user sent (it will NOT run)
# if we reach here without raising ReturnCommand, the user's command runs normally
```

- `raise ReturnCommand` inside `@` means **"handled — do not run the
  command."** This is a **normal completion**, not an error.
- If `@` finishes **without** raising `ReturnCommand`, the user's command runs as usual.
- `@` runs in its **own namespace** — variables set in `@` do **not** leak into the
  command (unlike `~`, which shares its namespace).
- Keep `@` **fast and defensive** — it runs on *every* interaction. Wrap anything risky
  (external calls, optional attributes) in `try/except` so a hiccup in `@` doesn't
  interfere with normal commands.

Use `@` for: force-subscribe gates, onboarding redirects (`Bot.runCommand("/start")`),
per-user rate limits, maintenance mode, blocking abusive users.

---

## `!` — Error handler (runs when a command errors)

When any command raises an **unhandled error**, the `!` command runs. It receives the
error text in the `options` variable, so you can show a friendly message, log it, or
alert an admin.

```python
# !  (error handler)
bot.sendMessage("⚠️ Something went wrong. Our team has been notified.")
# `options` holds the error message; the command that failed is available too.
```

- `runCommand(...)` is **disabled inside `!`** — this prevents infinite error loops.
- If you don't define `!`, the platform sends a generic error notice to the user.

Use `!` for: friendly error replies, error logging/analytics, admin alerts.

---

## `*` — Wildcard / fallback (runs when nothing matches)

When a user's message or command **doesn't match any command you defined**, the `*`
command runs. It's your catch-all.

```python
# *  (wildcard)
bot.sendMessage("I didn't understand that. Type /help to see what I can do.")
```

Use `*` for: default replies, "unknown command" messages, natural-language routing
(inspect `message.text` and decide what to do), keyword handlers.

---

## Execution order (putting it together)

For an incoming message `/foo` from a user:

1. **`@`** runs first (the gate). If it raises `ReturnCommand`, everything stops here.
2. **`~`** is prepended to `/foo`; the two run together as one program.
3. If **`/foo` isn't defined**, **`*`** runs instead (still with `~` on top).
4. If anything **raises an error**, **`!`** runs.

So a fully-configured bot flows: `@` (gate) → `~` + command (or `*`) → `!` (only on error).

---

## Quick reference

- `~` — *always prepended*, shared setup, runs inline, can't stop the command.
- `@` — *runs before* every command, `raise ReturnCommand` to skip the command, own namespace.
- `!` — *runs on error*, `options` = error text, `runCommand` disabled inside it.
- `*` — *runs when unmatched*, your catch-all default.
