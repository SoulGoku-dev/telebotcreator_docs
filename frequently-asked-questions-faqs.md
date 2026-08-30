# Frequently Asked Questions (FAQs)

*Telebot Creator Documentation — Platform v7.1.2 · Telegram Bot API 10.1*

*Last updated: June 2026 | Maintained by Telebot Creator Team*

Quick answers to the most common questions about Telebot Creator. If your question isn't covered here, ask in the [TBC Community Group](https://t.me/telebotcreatorbetachat).

---

## General Questions

### What is Telebot Creator?

Telebot Creator (TBC) is a free online platform for building, hosting, and managing Telegram bots. It uses TPY (Telebot Python), a custom scripting language based on Python, which includes over 30 built-in libraries for AI integration, cryptocurrency payments, data management, webhooks, and more. The platform hosts over 80,000 active bots serving more than 20 million Telegram users as of 2026. You don't need your own server — TBC handles all hosting, scaling, and infrastructure. Every new account receives 100,000 free execution points per month, making it completely free to build and run bots. Whether you're a complete beginner or an experienced developer, TBC provides the tools to create anything from simple auto-reply bots to complex AI-powered applications.

### Is Telebot Creator really free?

Yes, Telebot Creator is 100% free to use. Every new account receives 100,000 execution points per month, where each bot command execution costs just 1 point. This means you can handle 100,000 user interactions per month at no cost. There are no hidden fees, no credit card required, no premium tiers, and no limits on the number of bots you can create. If you need more points, you can request them for free from the admin team in the TBC Help Group on Telegram. The platform sustains itself through minimal, non-intrusive advertising — just 2-4 broadcast messages per month. All 30+ libraries, the code editor, broadcasting, webhooks, and every feature are available to all users at no charge.

### How do I create my first bot?

Creating your first bot on Telebot Creator takes less than 5 minutes. First, register a free account at telebotcreator.com. Next, open Telegram and message @BotFather — use the `/newbot` command to create a new bot and copy the API token it gives you. Then go to the TBC dashboard, click "Add New Bot," paste your token, and click "Create Bot." Your bot appears on the dashboard in Stopped status. Click on it, go to Commands, add a `/start` command with code like `bot.sendMessage("Hello!")`, save it, and click Start. Your bot is now live on Telegram. For a detailed walkthrough with code examples, see the [Getting Started guide](getting-started.md).

### What are the limitations of the free plan?

The free plan includes 100,000 execution points per month (1 point per command execution), a maximum of 2 simultaneous broadcasts per bot, and up to 160 seconds of execution time per command. There is a global limit of 1,000 simultaneous broadcasts across all platform users. Scheduled commands are limited to 100 per user per bot, with scheduling possible up to one year in advance. There are no limits on the number of bots you can create, the number of commands per bot, or which libraries you can use. All features including AI integration, blockchain libraries, webhooks, broadcasting, and the full Telegram Bot API are available to every user. If you need more points, simply request them for free from the admin team.

### How do I check my remaining points?

You can check your remaining points directly from your bot's code using the built-in `left_points` variable. Add a command like `/points` to your bot with this code:

```python
points = left_points
bot.sendMessage(f"You have {points} points remaining this month.")
```

You can also check your points from the TBC dashboard in the account settings section.

### Will there be advertisements in my bot?

Telebot Creator uses a minimal advertising model to keep the platform free for everyone. Advertisements appear only 2 to 4 times per month, delivered as a single broadcast message to your bot's users. These ads are non-intrusive and non-repetitive — they won't spam your users or interrupt their experience. This approach is fundamentally different from other bot platforms that insert ads into every bot response or show pop-ups. The TBC team prioritizes user experience, so advertising frequency is kept to an absolute minimum. This revenue model allows the platform to provide free hosting, free libraries, and free points to all users without requiring paid subscriptions or premium tiers.

---

## Commands and Features

### How do I create a command?

Commands are created through the Commands tab in your bot's dashboard. Click "Add Command," enter a command name (like `/start` or `/help`), and write your TPY code in the built-in editor. Every command must have a name and associated code.

```python
# Example /start command
bot.sendMessage("Welcome to my bot! Type /help to see available commands.")
```

Save the command, make sure your bot is started, and test it by messaging your bot on Telegram. Commands can be simple one-line responses or complex multi-step workflows using `Bot.handleNextCommand()`, `Bot.runCommand()`, and `Bot.runCommandAfter()`.

### What is the difference between handleNextCommand and runCommand?

These are two fundamental command flow tools in TBC. `Bot.handleNextCommand("command_name")` tells the bot to wait for the user's next message and then route that message to the specified command — this is how you create multi-step conversations, forms, and interactive flows. `Bot.runCommand("command_name")` executes another command immediately without waiting for user input — useful for redirecting flow, running shared logic, or building modular bots. There's also `Bot.runCommandAfter(seconds, "command_name")` which schedules a command to run after a delay, from 1 second up to 366 days. Understanding these three functions is essential for building any interactive Telegram bot on TBC.

### What are the special commands (* and @)?

TBC has two special command types. The **wildcard command (`*`)** triggers when a user sends any message that doesn't match a defined command. Use it for fallback responses, AI chatbot logic, or handling free-text input. The **at handler command (`@`)** runs before every other command — use it for preprocessing messages, logging user activity, checking banned users, or setting global variables. Together with `handleNextCommand`, these special commands give you complete control over how your bot handles every possible user interaction.

### Can I execute a command for another bot?

Yes, TBC supports cross-bot communication. You can use the `bot_id` and `api_key` parameters with functions like `libs.Webhook.getUrlFor()`, `Bot.runCommandAfter()`, and `Bot.broadcast()` to execute commands on another bot you own. This enables powerful multi-bot architectures where bots can coordinate actions, share data, and trigger each other's workflows. You need the target bot's ID and the account API key for authentication.

---

## Libraries and Integrations

### How do I integrate AI into my bot?

TBC includes built-in AI libraries for OpenAI and Google Gemini. Here's a quick example using OpenAI:

```python
client = libs.openai_lib.OpenAIClient(api_key="YOUR_OPENAI_KEY")
assistant = libs.openai_lib.AIAssistant(
    openai_client=client,
    model="gpt-4o",
    system_message="You are a helpful assistant."
)
response = assistant.send_message(msg)
bot.sendMessage(str(response.get("content")[0]['text']['value']))
```

TBC also supports Gemini via `libs.gemini_lib` and OpenRouter for access to 100+ AI models. AI commands can run up to 160 seconds, giving plenty of time for complex AI responses.

### How do I accept crypto payments?

Use the `libs.Coinbase` library for Coinbase Commerce payments:

```python
libs.Coinbase.setKeys("API_KEY", "SECRET")
client = libs.Coinbase.post()
payment = client.createCharge({
    "name": "Premium Subscription",
    "description": "Monthly access",
    "local_price": {"amount": "10.00", "currency": "USD"},
    "pricing_type": "fixed_price"
})
bot.sendMessage(f"Pay here: {payment['hosted_url']}")
```

For TON blockchain, use `libs.TonLib`. For EVM chains (Ethereum, Polygon, Arbitrum, etc.), use `libs.web3lib`.

### Can I use multiple libraries in a single bot?

Absolutely. TBC libraries are designed to work together. You can combine `libs.openai_lib` for AI responses, `libs.Coinbase` for payments, `libs.CSV` for data storage, `libs.Webhook` for real-time updates, and `libs.Resources` for user points — all in the same bot. There's no limit to how many libraries you can use simultaneously.

### How do I make HTTP requests to external APIs?

Use the built-in `HTTP` module (recommended) or `libs.customHTTP()`:

```python
# Using built-in HTTP (recommended since 4.9.0)
response = HTTP.get("https://api.example.com/data")
data = response.json()
bot.sendMessage(f"Result: {data}")

# POST request with JSON body
response = HTTP.post("https://api.example.com/submit", json={"key": "value"})
```

---

## Broadcasting

### How does broadcasting work?

Broadcasting sends a message or runs a command for all your bot's users at once. Use `Bot.broadcast()` with either a pre-defined Telegram function (like `send_message`, `send_photo`) or a custom command name. You can broadcast text, photos, videos, files, and more. Each bot can run 2 simultaneous broadcasts, and there's a global limit of 1,000 across all platform users.

```python
# Simple text broadcast
Bot.broadcast(function="send_message", text="Big announcement!")

# Run a command for all users
Bot.broadcast(command="send_promo")
```

### Why is my broadcast not working?

The most common reasons are: you already have 2 running broadcasts (the per-bot limit), the command name doesn't exist in your bot, or the broadcast function name is invalid. Check your running broadcasts with `Bot.listBroadcasts()` and stop any completed ones with `Bot.clearBroadcast()`.

---

## Webhooks

### What are webhooks and how do I use them?

Webhooks allow your bot to receive real-time data from external services like payment processors, form submissions, or any system that can send HTTP requests. Generate a webhook URL with `libs.Webhook.getUrlFor()`, give that URL to the external service, and TBC will automatically trigger the specified command when data arrives. Inside webhook commands, the incoming data is available in `options["json"]` and `options["data"]`.

```python
# Generate webhook URL
webhook_url = libs.Webhook.getUrlFor("payment_received", user_id=u)
bot.sendMessage(f"Webhook URL: {webhook_url}")
```

---

## Bot Management

### How do I transfer a bot to another account?

Use the `Bot.Transfer()` function in any command:

```python
result = Bot.Transfer(
    email="newowner@example.com",
    bot_id=bot_id,
    bot_token=bot_token,
    run_now=True
)
```

Points stay with the original account. The new owner needs their own points. Transferred bots cannot be retrieved unless the new owner transfers them back. Deleted bots can be recovered within 90 days.

### How do I manage multiple bots from one account?

Use the `Account` class (added in version 4.8.0). It provides account-level operations across all your bots:

```python
# List all bots in your account
bots = Account.get_bots_list()

# Save data accessible from any of your bots
Account.saveData("shared_config", {"setting": "value"})

# Get stats across all bots
stats = Account.getStats(time_frames=["24h", "7d"])
```

---

## Points and Billing

### How are points deducted?

Each command execution costs exactly 1 point. This includes any command trigger — `/start`, callback queries, inline queries, webhook triggers, scheduled commands, and broadcast executions. Making HTTP requests, saving data, and using libraries within a command don't cost extra points — only the initial command trigger counts.

### What happens if I run out of points?

Your bot stops responding to messages until points are replenished. Points renew every month, or you can request additional points at any time for free from the admin team in the TBC Help Group. There's no limit to how many points you can request.

---

## Troubleshooting

### Why is my bot not responding?

Common causes: the bot is in Stopped status (click Start), the command name doesn't match what the user sent, there's a syntax error in your TPY code (check Error Logs), or you've run out of points. Always check the Error Logs section in your bot's dashboard for specific error messages.

### How do I debug errors?

Use try-except blocks and check the Error Logs:

```python
try:
    result = some_operation()
    bot.sendMessage(f"Success: {result}")
except Exception as e:
    bot.sendMessage(f"Error occurred: {e}")
```

The Error Logs in your bot's dashboard show all runtime errors with timestamps, command names, and error details.
