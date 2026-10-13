---
description: >-
  BotFather guide: create a Telegram bot with /newbot, find or replace its token, and set the name, picture, command menu and group privacy.
---

# BotFather: Create a Bot and Get Its Token

BotFather is Telegram's official bot for creating and managing bots. Every Telegram bot starts there: BotFather gives the bot its username and its **token**, the secret key that lets a program answer as that bot. This page covers the commands you will really use.

## Open the real BotFather

Search for `@BotFather` in Telegram or open [t.me/BotFather](https://t.me/BotFather). The real one has a blue verification mark. Never send a token to any other account.

## Create a bot and get the token

1. Send `/newbot`.
2. Send a display name. This is what people see in the chat list, for example `Pizza Order Bot`.
3. Send a username. It must end in `bot` (for example `pizza_order_bot` or `PizzaOrderBot`), be 5 to 32 characters long, and use only Latin letters, numbers and underscores.
4. BotFather answers with the token. It looks like `7123456789:AAFoXXR2e-Token-Example`: the bot's ID, a colon, and a long secret.

To put the bot online, paste the token into Telebot Creator: **Add New Bot** on the dashboard. The [quick start](getting-started.md) shows the rest, and hosting is free.

> One Telegram account can own up to 20 bots.

## Find your token again

Send `/mybots`, choose the bot and press **API Token**. BotFather shows the same token again.

## Replace a leaked token

Anyone who has the token can control the bot. If it was posted in a chat, in a screenshot or in public code, replace it:

1. Send `/mybots`, choose the bot, press **API Token**, then **Revoke current token** (the command `/revoke` does the same).
2. The old token stops working at once and BotFather shows a new one.
3. In Telebot Creator open the bot, go to **Settings**, and paste the new token.

## BotFather commands worth knowing

| Command | What it does |
|---|---|
| `/newbot` | Creates a bot |
| `/mybots` | Lists your bots, with buttons for every setting |
| `/setname` | Changes the display name |
| `/setdescription` | The text people see before they press Start (up to 512 characters) |
| `/setabouttext` | The short text on the bot's profile (up to 120 characters) |
| `/setuserpic` | The profile picture |
| `/setcommands` | The command list in the Telegram menu |
| `/setprivacy` | Whether the bot sees every message in a group or only commands |
| `/setjoingroups` | Whether the bot can be added to groups |
| `/setinline` | Turns inline mode on or off |
| `/token` | Shows the token |
| `/revoke` | Replaces the token with a new one |
| `/deletebot` | Deletes the bot for good |

### The command menu

`/setcommands` fills the menu that opens when a user types `/`. Send one command per line, in lowercase and without the slash, then a dash and a short description:

```
start - Open the main menu
help - How to use the bot
balance - Show my coins
```

The menu is only a list. Each command still needs its code: on Telebot Creator that is a command with the same name, for example `/start`.

### Group privacy

By default a bot in a group receives only commands, replies to its own messages and service messages. To let it read every message, send `/setprivacy`, choose the bot and press **Disable**, then remove the bot from the group and add it again. A bot that is an administrator of the group always receives every message. More in [How to Add a Bot to a Telegram Group or Channel](how-to-add-a-bot-to-a-telegram-group.md).

## Common questions

**Is BotFather free?** Yes. Creating and managing bots costs nothing.

**BotFather says the username is taken.** Usernames are unique across Telegram. Try a longer or more specific one.

**Is it safe to paste my token into a hosting platform?** A platform needs the token to answer for your bot: that is how every bot host works. You stay the owner in BotFather and can revoke the token at any time.
