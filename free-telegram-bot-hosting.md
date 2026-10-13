---
description: >-
  Free Telegram bot hosting with no server: your bot runs 24/7 on Telebot Creator with unlimited points, data storage, scheduling and broadcasts.
---

# Free Telegram Bot Hosting

Telebot Creator hosts Telegram bots for free. You write the bot's commands in the browser, or let the AI assistant write them, press **Start**, and the bot answers day and night. There is no server to rent, nothing to deploy and no bill. More than 240,000 bots are running on the platform as of October 2026.

## What hosting means for a Telegram bot

A bot is a program that has to be online all the time to answer messages. Normally you rent a server, install your code, connect it to Telegram and keep it running. On Telebot Creator that part is done for you: Telegram delivers each message to the platform, the platform runs the command that matches, and the answer goes back to the user.

## What is included

| | |
|---|---|
| **Price** | Free. Points are unlimited and a command costs 0 points |
| **Bots** | No limit on the number of bots |
| **Always on** | The bot runs 24/7. Nothing has to stay open on your computer or phone |
| **Data storage** | Data per user and per bot with `User.saveData` and `Bot.saveData`, balances with `libs.Resources` |
| **Scheduling** | `Bot.runCommandAfter` runs a command later, from 1 second to 366 days ahead |
| **Broadcasts** | Send a message to every user of the bot |
| **Libraries** | 30+ built in: web requests, AI, payments, crypto, CSV and more |
| **Run time** | Up to 160 seconds for one command |
| **Tools** | Code editor, error log, AI assistant, Android app |

## What you cannot host here

Telebot Creator runs bots written in TPY, its Python-based language, as commands in its own editor. It does not run a project you upload. A bot written with aiogram, python-telegram-bot, Telegraf or another framework has to be rewritten as commands first. There is also no file system and no background process: a command runs when a message arrives, or at the time you scheduled it.

If you need your own framework or full control of the machine, rent a server. If you want a bot that works without looking after a server, this is the shorter road.

## Host your bot in three steps

1. [Create a free account](https://telebotcreator.com/register).
2. Create a bot in [BotFather](botfather-and-bot-token.md) and paste its token into **Add New Bot**.
3. Write the commands, or describe the bot to the [AI assistant](ai-telegram-bot-builder.md), and press **Start**.

The [quick start](getting-started.md) shows each step with code.

## Telebot Creator or your own server

| | Telebot Creator | Your own server |
|---|---|---|
| Cost | Free | A monthly bill |
| Setup | Paste a token | Install the language, the libraries, a webhook or polling, a process manager |
| Changing the bot | Save a command and it is live | Deploy again |
| Code from another framework | No, TPY only | Yes |
| Control of the machine | No | Yes |

## How it stays free

The platform shows an advertisement to a bot's users 2 to 4 times a month, as one broadcast message. Bot owners can also switch on [rewarded ads](monetization.md) and earn when their users choose to watch one.

## Common questions

**Does my bot stop when I close the browser?** No. The bot runs on the platform, not on your device.

**Do I need to know Python?** It helps, but it is not required. The [AI assistant](ai-telegram-bot-builder.md) writes and fixes the commands from a chat.

**Is there a paid plan?** No. Every feature is free for every account.
