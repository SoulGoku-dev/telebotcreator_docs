# Frequently Asked Questions (FAQs)

#### **10. Frequently Asked Questions (FAQs)**

This section addresses common questions and concerns about using Telebot Creator. Whether you're a beginner or an experienced user, these FAQs provide quick answers to help you use the platform effectively.

***

### **10.1 General Questions**

#### **1. What is Telebot Creator, and how does it work?**

Telebot Creator is a platform for building and hosting Telegram bots. It uses a custom programming language called TPY (Telebot Python) to simplify bot creation, allowing you to add commands, integrate libraries, and host bots without managing your own servers.

#### **2. How do I create my first bot?**

To create your first bot:

1. Get a Bot API token from @BotFather on Telegram.
2. Log in to Telebot Creator.
3. Click **"Add New Bot"** on the dashboard and paste the API token.
4. Start adding commands and customizing your bot.

#### **3. What are the limitations of the free plan?**

The free plan includes:

* 100,000 points per month for bot operations.
* Each command execution costs 1 point.
* A maximum of 2 simultaneous broadcasts per bot.

#### **4. How do I check my remaining points?**

Use the following code to display your remaining points:

```python
points = left_points
bot.sendMessage(f"You have {points} points remaining.")
```

#### **5. Will there be advertisements in my bot?**

Telebot Creator has a minimal advertising policy:
* Ads appear only 2-4 times per month as a single broadcast message.
* These are non-intrusive and won't spam your users.
* The platform prioritizes user experience by keeping advertisements to an absolute minimum.

#### **6. How can I get more points if I run out?**

You can obtain more points in several ways:
* Request additional points for free by asking admins in the TBC Help Group.
* In upcoming updates, a points faucet will allow you to obtain unlimited points.
* All additional points are provided completely free of charge.

#### **7. What is the upcoming Bot Store?**

The Bot Store is a new feature coming in the next update that will allow users to:
* Discover pre-made bots for various purposes.
* Share their own bot templates with the community.
* Deploy ready-to-use bots without having to code them from scratch.
* Access specialized bot templates for different industries and use cases.

***

### **10.2 Commands and Features**

#### **1. How do I create a command?**

Commands are added via the "Commands" menu in the bot dashboard. For example, to create a `/start` command:

1. Open the **Commands** menu.
2. Click **"Add Command"**.
3.  Define the command name (`/start`) and write its logic in TPY:

    ```python
    bot.sendMessage("Welcome to the bot!")
    ```

#### **2. What is the difference between `handleNextCommand` and `runCommand`?**

*   **`handleNextCommand`**: Waits for the user's next message and then routes it to a specific command.

    ```python
    bot.sendMessage("What's your name?")
    Bot.handleNextCommand("save_name")
    ```
*   **`runCommand`**: Executes another command immediately.

    ```python
    bot.sendMessage("Redirecting to the help menu...")
    Bot.runCommand("help")
    ```

#### **3. Can I execute a command for another bot?**

Yes, you can use the `bot_id` and `api_key` parameters with certain functions like `libs.Webhook.getUrlFor` to execute commands for another bot.

***

### **10.3 Libraries and Integrations**

#### **1. How do I integrate payments using Coinbase?**

Set up the Coinbase library:

```python
libs.Coinbase.setKeys("API_KEY", "SECRET")
client = libs.Coinbase.post()
```

Create a payment request:

```python
payment = client.createCharge({
    "name": "Subscription",
    "description": "Monthly fee",
    "local_price": {"amount": "10.00", "currency": "USD"},
    "pricing_type": "fixed_price"
})
bot.sendMessage(f"Pay here: {payment['hosted_url']}")
```

#### **2. Can I use multiple libraries in a single bot?**

Yes, multiple libraries can be combined seamlessly. For example, you can use `libs.CSV` for data storage and `libs.Webhook` for real-time updates in the same bot.

#### **3. How do I fetch external data using `libs.customHTTP`?**

Use the `get` method to fetch data:

```python
http_client = libs.customHTTP()
response = http_client.get("https://api.example.com/data")
bot.sendMessage(f"API Response: {response.json()}")
http_client.close()
```

***

### **10.4 Broadcasting**

#### **1. Why is my broadcast not working?**

Common reasons:

1. You've exceeded the maximum of 2 running broadcasts per bot.
2. The `function` or `command` used in the broadcast is invalid.

#### **2. What are the limits for broadcasting?**

* **User Limit**: 2 simultaneous broadcasts per bot.
* **Global Limit**: 1000 simultaneous broadcasts across all bots.

#### **3. How do I test a broadcast before sending it?**

Use the `broadcast` function with a test run:

```python
Bot.broadcast(
    function="send_message",
    text="Testing broadcast system."
)
```

***

### **10.5 Webhooks**

#### **1. What is a webhook, and how does it work in Telebot Creator?**

A webhook is a URL that allows your bot to receive real-time updates from external systems or trigger commands dynamically. Use `libs.Webhook.getUrlFor` to generate webhook URLs.

#### **2. How do I generate a webhook URL?**

```python
webhook_url = libs.Webhook.getUrlFor(
    command="process_data",
    user_id=12345
)
bot.sendMessage(f"Webhook URL: {webhook_url}")
```

#### **3. How do I secure my webhook endpoints?**

* Use dynamically generated webhook URLs.
* Validate requests by checking headers or access tokens.

***

### **10.6 Bot Transfer and Management**

#### **1. How do I transfer a bot to another account?**

Use the `Bot.Transfer` function:

```python
result = Bot.Transfer(
    email="newowner@example.com",
    bot_id="123456",
    bot_token="BOT_API_TOKEN",
    run_now=True
)
bot.sendMessage(f"Bot transferred successfully: {result['bot_id']}")
```

#### **2. What happens to my points after transferring a bot?**

Points remain with the original account. The new owner will need points in their account to run the transferred bot.

#### **3. Can I retrieve a transferred bot?**

No, once a bot is transferred, it cannot be retrieved unless the new owner transfers it back.

***

### **10.7 Payment and Points**

#### **1. How do I purchase more points?**

Points are completely free. You can request additional points by:
* Asking admins in the TBC Help Group.
* In future updates, using the points faucet to obtain unlimited points.

#### **2. How are points deducted for bot actions?**

Each command execution costs 1 point. Broadcasts and API integrations may consume additional points based on usage.

#### **3. What happens if I run out of points?**

Your bot will stop functioning until points are replenished. You can easily request more points from the TBC Help Group admins at any time, free of charge.

#### **4. How many points do new accounts receive?**

New accounts automatically receive 100,000 points upon creation, enough to execute 100,000 commands.

#### **5. Is there a limit to how many points I can request?**

No, you can request as many points as you need to run your bots. The platform is designed to be generous with points to ensure you can operate your bots without restrictions.

***

### **10.8 Troubleshooting**

#### **1. Why is my bot not responding?**

* **Cause**: Command not defined, or bot is not running or maybe the server is down.
* **Solution**: Check the command list in the dashboard and start the bot if it's stopped.

#### **2. What should I do if my webhook fails?**

* **Cause**: Invalid webhook URL or unreachable endpoint.
* **Solution**: Regenerate the webhook URL using `libs.Webhook.getUrlFor` and verify the endpoint.

#### **3. How do I debug errors in my bot's commands?**

Use try-except blocks to catch and log errors:

```python
try:
    bot.sendMessage("Testing risky operation...")
except Exception as e:
    bot.sendMessage(f"Error: {e}")
```

***
