# Introduction to Telebot Creator

*Telebot Creator Documentation — Platform v7.1.2 · Telegram Bot API 10.1*

*Last updated: June 2026 | Maintained by Telebot Creator Team*

## What is Telebot Creator?

Telebot Creator (TBC) is a **free platform for building, hosting, and managing Telegram bots**. You don't need your own server, you don't need to pay anything, and you can go from zero to a live bot in under 5 minutes.

TBC uses **TPY (Telebot Python)**, a custom scripting language based on Python. TPY comes with **30+ built-in libraries** for AI, payments, blockchain, data management, webhooks, and more — so you can build powerful bots without installing anything.

### Platform Statistics (2026)

| Metric | Value |
|---|---|
| **Active Bots** | 80,000+ |
| **Total Bots Created** | 150,000+ |
| **Telegram Users Served** | 20,000,000+ |
| **TBC Platform Version** | 7.1.2 |
| **Telegram Bot API** | 10.1 |
| **Libraries** | 30+ built-in |

> **Two version numbers, two different things.** The **TBC platform version** (currently **7.1.2**) tracks the Telebot Creator product itself — its TPY runtime, libraries, dashboard, and services. The **Telegram Bot API version** (currently **10.1**) tracks how much of Telegram's official Bot API the `bot` object supports. They are independent: a platform release can ship without changing the Bot API level, and a Bot API bump can ship without a platform version change.

---

## Quick Start — Create a Bot in 5 Minutes

1. **Register** — Create a free account at [telebotcreator.com](https://telebotcreator.com/register).
2. **Get a Bot Token** — Open Telegram, message [@BotFather](https://t.me/BotFather), use `/newbot`, and copy the API token.
3. **Add Your Bot** — On the TBC dashboard, click **"Add New Bot"**, paste your token, and click **"Create Bot"**.
4. **Write Your First Command** — Click on your bot, go to Commands, add a `/start` command with this code:
   ```python
   bot.sendMessage("Hello! Welcome to my bot 🚀")
   ```
5. **Start Your Bot** — Click the Start button. Your bot is now live on Telegram!

> **Need help?** Join the [TBC Community Group](https://t.me/telebotcreatorbetachat) on Telegram.

---

## What Are Commands?

Commands are the building blocks of every TBC bot. When a Telegram user sends a message like `/start` or `/help`, your bot runs the TPY code you wrote for that command.

```python
# /start command example
first_name = message.from_user.first_name
bot.sendMessage(f"Hey {first_name}! Welcome to my bot. Use /help to see what I can do.")
```

Commands can:
- Send text, photos, videos, files, stickers, and more
- Ask for user input and process it step by step
- Store and retrieve user data
- Make HTTP requests to external APIs
- Accept payments via crypto or traditional gateways
- Run AI models (GPT-4, Gemini, etc.)
- Schedule tasks up to 1 year in the future
- Broadcast messages to all your bot's users

---

## What is TPY?

**TPY (Telebot Python)** is TBC's custom scripting language. If you know basic Python, you already know TPY. If you don't know Python, TPY is simple enough to learn in a few hours.

**Key features:**
- **Python-based syntax** — Variables, if/else, loops, functions, try/except all work the same way
- **30+ built-in libraries** — AI (OpenAI, Gemini), payments (Coinbase, TON), data (CSV, Resources), HTTP, webhooks, and more
- **Pre-defined variables** — `msg`, `message`, `bot`, `Bot`, `User`, `Account`, `params`, `u`, `options` are ready to use
- **Sandboxed environment** — Each bot runs in isolation. No `eval()`, `exec()`, or system module access for security
- **Up to 160 seconds** execution time per command

---

## Points System — It's Free

TBC uses a points system for bot hosting. Here's the deal:

| Feature | Details |
|---|---|
| **New Account Points** | 100,000 points free |
| **Cost Per Command** | 1 point |
| **Monthly Renewal** | Yes, every month |
| **Extra Points** | Request free from admins anytime |
| **Hidden Fees** | None — it's genuinely free |

Each command execution (like responding to `/start`) costs 1 point. That means a new account can handle **100,000 command executions per month** for free.

### Advertising Policy

TBC keeps the platform free through minimal, non-intrusive advertising:
- Ads appear only **2-4 times per month** as a single broadcast message
- No continuous spam or pop-ups
- Your bot users get an uninterrupted experience

---

## Key Features at a Glance

| Feature | Description |
|---|---|
| **Free Hosting** | No servers to manage. TBC hosts your bots for free. |
| **AI Integration** | Built-in OpenAI (GPT-4o), Gemini, and OpenRouter support |
| **Crypto Payments** | Coinbase Commerce, TON blockchain, Web3 (all EVM chains) |
| **Broadcasting** | Send messages to all bot users at once |
| **Scheduled Commands** | Schedule actions from 1 second to 366 days ahead |
| **Webhooks** | Real-time integrations with external services |
| **Data Storage** | User-level and bot-level data storage, CSV files, Resources system |
| **Multi-Bot Management** | Manage all bots from one account using the Account class |
| **Bot Transfer** | Transfer bot ownership to another account |
| **Bot Recovery** | Recover deleted bots within 90 days |
| **Inline Queries** | Handle inline mode, callback queries, payments, and all Telegram update types |
| **Telegram Bot API 10.1** | Full coverage of the latest Telegram features — Gifts, Stories, Business accounts, Checklists, Suggested posts, Verification, and Stars |
| **Image Processing** | OpenCV and Pillow libraries for image manipulation |
| **Code Editor** | Built-in TPY code editor with syntax highlighting and autocomplete |
| **Bot Export / Import** | Export a bot to a portable file, import it back, and round-trip through the AI assistant |
| **Docs MCP Server** | Public Model Context Protocol endpoint so any AI assistant can read the live TBC docs |

---

## Built-in Libraries

TBC includes 30+ libraries you can use directly in your code — no installation needed:

| Category | Libraries |
|---|---|
| **AI** | `libs.openai_lib` (GPT-4o, Assistants API), `libs.gemini_lib` (Gemini Flash/Pro), OpenRouter support |
| **Payments** | `libs.Coinbase`, `libs.Coinpayments`, `libs.Oxapay`, `libs.MDxchange` |
| **Blockchain** | `libs.TonLib` (TON), `libs.web3lib` (all EVM chains), `libs.Crypto` |
| **Data** | `libs.CSV`, `libs.Resources` (points, credits, leaderboards) |
| **Media** | `libs.OpenCV`, `libs.Pillow` |
| **HTTP** | `libs.customHTTP`, built-in `HTTP` module |
| **Webhooks** | `libs.Webhook` |
| **Utilities** | `libs.Random`, `libs.DateAndTime` |

---

## The New UI (Version 2)

Telebot Creator now features a modern, redesigned dashboard:

- **Dark theme** with a clean, professional design
- **Bot management** — Add, start, stop, and delete bots from one screen
- **Command editor** — Built-in TPY code editor with syntax highlighting, autocomplete, and AI-powered code suggestions
- **Error logs** — View and debug command errors with timestamps
- **Settings** — Update bot tokens, manage transfers, and configure bot options
- **Notifications** — Real-time alerts for bot events
- **Mobile-friendly** — Fully responsive design that works on all devices

---

## Community & Support

- **Telegram Help Group**: [t.me/telebotcreatorbetachat](https://t.me/telebotcreatorbetachat)
- **Documentation**: [help.telebotcreator.com](https://help.telebotcreator.com)
- **ChatGPT AI Assistant**: [Telebot Creator AI GPT](https://chatgpt.com/g/g-67ce8f8e7da081918cc244a92dc5aa55-telebot-creator-ai)
- **Website**: [telebotcreator.com](https://telebotcreator.com)
