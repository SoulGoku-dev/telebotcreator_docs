---
description: >-
  Build a Telegram bot by chatting with an AI: Telebot Creator AI writes the commands, tests them, reads the errors and fixes them. Free.
---

# AI Telegram Bot Builder

Telebot Creator AI is an assistant inside the dashboard that builds Telegram bots from a chat. You describe the bot in plain words. It writes the commands, runs them as a test, reads the errors and fixes them. It is free for every account.

## Build a bot in three steps

1. Create a bot in [BotFather](botfather-and-bot-token.md) and add its token on the dashboard (**Add New Bot**).
2. Open **Telebot Creator AI** in the menu. On a phone it is the **AI** tab.
3. Say what you want. The AI asks when something is unclear, then builds it.

## Things you can ask

- "Make a welcome bot with a menu of three buttons: Prices, Contact and FAQ."
- "Add a referral system: every user gets an invite link and 5 coins for each friend who joins."
- "My /buy command shows an error. Find the cause and fix it."
- "Add a rewarded ad: the user watches an ad and gets 10 coins."
- "Explain what the /withdraw command does, step by step."

You can attach a picture, for example a screenshot of an error or of a menu you want copied.

## What it does for you

- Writes new commands and edits the ones you have. The code is checked before it is saved.
- Runs a command as a test and reads the result.
- Reads the bot's runtime errors and fixes the cause.
- Reads the bot's statistics, stored data and ad earnings to answer questions about the bot.
- Restores a command that was deleted.
- Sends a message or starts a broadcast when you ask for it.

## You decide how much it may do alone

The switch sits inside the message box.

| Mode | What it means |
|---|---|
| **Normal** | Asks before risky actions, such as deleting a command or stopping a bot |
| **Auto** | Decides when to ask. Deleting a bot and sending a broadcast always wait for your approval |
| **Bypass** | Never asks. Use it only when you are sure |

## Privacy and limits

- The AI works only with the bots of your own account.
- Bot tokens and keys inside your code are hidden from the AI.
- There is a daily limit. The chat shows how much of it you have used, as a percentage, and it starts again the next day.

## Prefer your own AI tool?

AI assistants and code editors that support MCP can build bots in your account too. See [MCP Server — Build Bots With AI](mcp-server.md).

## Common questions

**Do I need to know how to code?** No. You can read the code it writes, or just test the bot in Telegram.

**Can it work on a bot I already have here?** Yes. It reads the existing commands before it changes anything.

**Is it free?** Yes, within the daily limit.
