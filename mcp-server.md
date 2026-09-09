# MCP Server — Build Bots With AI

Connect Telebot Creator to Claude, Cursor, or any MCP-compatible AI and build
your bots by describing what you want. Your AI can create bots, write commands,
**actually run them**, read the errors, and fix them — all inside your own
account.

Free for every user.

---

## Connect in 3 steps

**1.** Add this URL to your AI as a custom connector:

```
https://api.telebotcreator.com/v2/mcp/build
```

**2.** Click **Connect**.

**3.** Paste your API key on the page that opens.

Your key is in the dashboard under **Settings → API Key**. That's it — your AI is
now linked to your account.

> **Docs only, no key needed.** If you just want your AI to read the Telebot
> Creator documentation, use `https://api.telebotcreator.com/v2/mcp` instead.
> It connects instantly with no sign-in and gives read-only doc access.

---

## What your AI can do

### Build

| Tool | What it does |
|---|---|
| `create_bot` | Import a bot into your account with its BotFather token |
| `clone_bot` | Copy a whole bot — every command — into a new one |
| `save_command` | Create or update one command |
| `import_commands` | Create or update many commands at once |
| `delete_command` | Move a command to the recycle bin (recoverable) |
| `validate_tpy` | Syntax-check code before saving |

### Test

| Tool | What it does |
|---|---|
| `test_command` | **Really runs** a command and reports what happened |
| `get_errors` | Recent runtime errors: command, message and line |
| `get_pending_wait` | Check if a user is mid-flow in `handleNextCommand` |
| `clear_wait` | Cancel a stuck flow so it can be retested |
| `send_message` | Send a real message from your bot |

### Manage

| Tool | What it does |
|---|---|
| `list_bots` | Every bot on your account |
| `get_bot` | One bot's details and running state |
| `list_commands` | Every command name on a bot |
| `get_command` | One command's source code |
| `start_bot` / `stop_bot` | Start or stop receiving updates |
| `rename_bot` | Change the display name |
| `get_bot_stats` | Total users, points used, command count |
| `delete_bot` | Permanently delete (requires confirmation) |

### Learn

| Tool | What it does |
|---|---|
| `list_docs` | Every documentation page |
| `search_docs` | Search the docs |
| `get_doc` | Fetch one page |
| `get_full_docs` | The whole documentation at once |

The documentation is built in, so your AI writes correct TPY from the first try
instead of guessing. That includes the **Monetization** page, so you can simply
ask for ads and get working code:

```
"Add a rewarded ad to my daily bonus command"
```

Your AI will write both halves — the `libs.tbcads.reward_ad(...)` call and the
reward command it points at, with the `libs.tbcads.claim()` guard that stops
anyone collecting the reward by typing the command name. Ask it to read
`monetization.md` first if you want it to follow the full pattern.

---

## Just say what you want

```
"Create an airdrop bot with referral tracking"
"Add a daily bonus command to my bot"
"Add a rewarded ad before the download and give 20 points when it is watched"
"Why is my /start command failing?"
"Clone my bot and add a language picker"
"Show me the errors on my bot from today"
```

Your AI will find the bot, write the code, save it, run it, read the error if
there is one, and fix it — then tell you it works.

---

## Safe by design

- **Your account only.** Every action is scoped to bots you own. There is no path
  to anyone else's bot.
- **Broken code never goes live.** Code is validated before saving and refused
  with the exact reason if it will not compile.
- **Your edits are respected.** Your AI reads the current code before changing
  anything, so edits you made in the web editor are never overwritten.
- **Nothing is silently deleted.** Saves and imports are additive. Deleting a
  command puts it in the recycle bin; deleting a bot requires explicit
  confirmation.

---

## Testing is what makes it work

Most AI tools write code and hope. `test_command` feeds a real Telegram update
through the same path a live message takes, so your AI sees the actual runtime
error — undefined names, wrong arguments, Telegram API errors — and fixes them
before you ever try the bot yourself.

One thing to know when reading results: **"DID NOT RUN" is not a pass.** It means
the update never reached the bot. Only a successful run with no recorded errors
counts as working.

---

## Other ways to authenticate

Most people use the Connect flow above. If your client can set custom headers,
these also work on `https://api.telebotcreator.com/v2/mcp`:

```
Authorization: Bearer YOUR_API_KEY
X-API-Key: YOUR_API_KEY
```

Keep your API key private — it grants full access to your bots. You can
regenerate it any time from **Settings → API Key**, which instantly invalidates
the old one.

---

## Troubleshooting

**Only 4 tools appear.** You are connected to the public docs endpoint. Use
`/v2/mcp/build` and complete the Connect step to get all 24.

**"That API key was not recognised."** Copy the key again from **Settings → API
Key** — it may have been regenerated.

**Your AI writes `return` at the top of a command.** TPY commands are flat
scripts, not functions. Tell it to use `raise ReturnCommand` instead. The server
refuses this automatically, so it can never reach a live bot.
