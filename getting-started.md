# Getting Started

*Telebot Creator Documentation — Platform v7.1.2 · Telegram Bot API 10.1*

This guide walks you through creating your first Telegram bot on Telebot Creator, from registration to a working bot. No prior programming experience required.

---

## Step 1: Create Your Account

1. Go to [telebotcreator.com/register](https://telebotcreator.com/register)
2. Enter your email address and choose a strong password
3. Complete the CAPTCHA verification
4. Click **Register**
5. Log in with your email and password

You'll land on the **Dashboard** — your home base for managing all your bots.

> **Free account includes**: 100,000 execution points per month, unlimited bots, access to all 30+ libraries.

---

## Step 2: Get a Bot Token from Telegram

Before adding a bot to TBC, you need a **Bot API token** from Telegram:

1. Open Telegram and search for [@BotFather](https://t.me/BotFather)
2. Send `/newbot`
3. BotFather will ask you for:
   - A **display name** for your bot (e.g., "My Cool Bot")
   - A **username** — must end with `bot` (e.g., `MyCoolTestBot`)
4. BotFather will give you a token like: `7123456789:AAFoXXR2e-Token-Example`
5. **Copy this token** — you'll paste it into TBC in the next step

> **Keep your token secret!** Anyone with your token can control your bot.

---

## Step 3: Add Your Bot to Telebot Creator

1. On the TBC dashboard, click the **"+" button** or **"Add New Bot"**
2. Paste your Bot API token
3. Click **"Create Bot"**

Your bot now appears on the dashboard with its name, username, and status. It starts in **Stopped** status — that's normal.

---

## Step 4: Create Your First Command

Let's make your bot respond when someone sends `/start`:

1. Click on your bot to open it
2. Go to the **Commands** tab
3. Click **"Add Command"**
4. Set the command name to `/start`
5. In the code editor, write:

```python
first_name = message.from_user.first_name
bot.sendMessage(f"Hello {first_name}! 👋 Welcome to my bot.\n\nUse /help to see what I can do.")
```

6. Click **Save**

Now let's add a `/help` command:

```python
bot.sendMessage("""
📋 Available Commands:

/start - Welcome message
/help - Show this menu
/about - About this bot
""")
```

---

## Step 5: Start Your Bot

1. Go back to your bot's main page
2. Click the **"Start"** button
3. The bot status changes to **"Working"**

Now open Telegram, find your bot by its username, and send `/start`. You should see your welcome message! 🎉

---

## Understanding the Dashboard

### Bot Status Indicators

| Status | Meaning |
|---|---|
| **Working** | Bot is active and responding to messages |
| **Stopped** | Bot is inactive. Stop your bot when editing commands. |
| **Cloned Bot** | Bot was cloned from another bot. Set a token in Settings to activate. |
| **Transferred Bot** | Bot was transferred from another account. Set the token in Settings. |

### Dashboard Sections

- **Bots List** — See all your bots, search, and manage them
- **Bot Detail** — Click any bot to see its commands, errors, and settings
- **Commands** — Add, edit, and delete bot commands. Each command has its own TPY code.
- **Error Logs** — View runtime errors with timestamps and details for debugging
- **Settings** — Update your bot token, transfer or clone bots, and configure options
- **Notifications** — Real-time alerts about your bots

---

## Your First Interactive Bot

Let's build something more interesting — a bot that asks for the user's name and remembers it:

### Command: `/start`
```python
bot.sendMessage("Hi there! What's your name?")
Bot.handleNextCommand("save_name")
```

### Command: `save_name`
```python
name = msg
User.saveData("name", name)
bot.sendMessage(f"Nice to meet you, {name}! I'll remember that.\n\nSend /myname anytime to see it.")
```

### Command: `/myname`
```python
name = User.getData("name")
if name:
    bot.sendMessage(f"Your name is: {name}")
else:
    bot.sendMessage("I don't know your name yet! Send /start to tell me.")
```

This demonstrates three key concepts:
- **`Bot.handleNextCommand()`** — Waits for the user's next message and routes it to another command
- **`User.saveData()`** — Stores data for this specific user
- **`User.getData()`** — Retrieves previously saved user data

---

## What's Next?

Now that your bot is running, explore these topics:

1. **[Commands in TPY](command-in-tpy.md)** — Learn command syntax, variables, parameters, and chaining
2. **[TPY Language Reference](tpy-language-reference.md)** — Full reference for all built-in functions, classes, and globals
3. **[Libraries](tbc-libraries-libs.md)** — Explore 30+ libraries for AI, payments, data, and more
4. **[Real-World Use Cases](real-world-use-cases.md)** — Build referral bots, payment bots, AI chatbots, and more
5. **[FAQ](frequently-asked-questions-faqs.md)** — Common questions and quick answers

> **Need help?** Join the [TBC Community Group](https://t.me/telebotcreatorbetachat) on Telegram.
