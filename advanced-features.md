# Advanced Features

#### **7. Advanced Features**

Telebot Creator offers powerful advanced features that extend basic bot functionality. This section explores these capabilities, including custom data storage, scheduled tasks, integration with external systems, and managing user interactions, among others.

***

### **7.1 Scheduled Commands**

Telebot Creator allows you to schedule commands to run at specific intervals, creating chatbots that can perform time-based tasks.

#### **Bot.runCommandAfter**

The `Bot.runCommandAfter` function lets you schedule a command to run after a specific time delay.

***

**Function Syntax**:

```python
Bot.runCommandAfter(
    delay_seconds: float,
    command: str,
    user_id: Optional[str] = None,
    chat_id: Optional[str] = None,
    params: Optional[str] = None,
    bot_id: Optional[str] = None,
    api_key: Optional[str] = None
)
```

> **New in 4.9.0**:
> - Maximum timeout extended to 1 year (365 days)
> - Minimum timeout reduced to 0.1 seconds
> - Smart rate limiting: For ultra-fast commands (under 0.4 seconds), a limit of 5 executions within 5 seconds to prevent abuse
> - Increased maximum scheduled commands per user from 20 to 100

**Parameters**:

* **`delay_seconds`** (_float_): The delay in seconds before executing the command. Range: 0.1 to 31,536,000 (365 days).
* **`command`** (_str_): The command to execute after the delay.
* **`user_id`** (_Optional\[str]_): The user ID for which to execute the command.
* **`chat_id`** (_Optional\[str]_): The chat ID in which to execute the command.
* **`params`** (_Optional\[str]_): Parameters to pass to the command.
* **`bot_id`** (_Optional\[str]_): The bot ID for which to schedule the command.
* **`api_key`** (_Optional\[str]_): The API key for authentication with external bots.

**Examples**:

1.  **Basic Scheduling**:

    ```python
    Bot.runCommandAfter(300, "reminder")  # Run the reminder command after 5 minutes
    ```
2.  **User-Specific Scheduling**:

    ```python
    Bot.runCommandAfter(3600, "daily_check", user_id=12345)  # Run for specific user after 1 hour
    ```
3.  **With Parameters**:

    ```python
    Bot.runCommandAfter(60, "send_reminder", params="meeting")  # Pass parameters to the command
    ```
4.  **Long-term Scheduling**:

    ```python
    # Schedule a command to run in 30 days (new in 4.9.0)
    Bot.runCommandAfter(2592000, "monthly_report") 
    ```
5.  **Ultra-fast Task**:

    ```python
    # Quick follow-up message (subject to rate limiting in 4.9.0)
    Bot.runCommandAfter(0.2, "send_followup") 
    ```

***

#### **7.2 Command Chaining for Workflows**

Command chaining allows bots to create step-by-step workflows, guiding users through complex processes such as registration, surveys, or multi-step forms.

**Example: Multi-Step Registration**

1.  **Start the Workflow**:

    ```python
    bot.sendMessage("Welcome! Let's start with your name.")
    Bot.handleNextCommand("get_name")
    ```
2.  **Process Name**:

    ```python
    name = msg
    User.saveData("name", name)
    bot.sendMessage(f"Thanks, {name}! What is your email?")
    Bot.handleNextCommand("get_email")
    ```
3.  **Complete Registration**:

    ```python
    email = msg
    User.saveData("email", email)
    bot.sendMessage("Your registration is complete!")
    ```

***

#### **7.3 Custom API Integrations**

With `libs.customHTTP`, bots can interact with external APIs, enabling dynamic data fetching or triggering external processes.

**Example: Weather Bot**:

```python
http_client = libs.customHTTP()
response = http_client.get("https://api.weatherapi.com/v1/current.json?key=API_KEY&q=New York")
weather_data = response.json()
bot.sendMessage(f"The current temperature in New York is {weather_data['current']['temp_c']}°C.")
http_client.close()
```

***

#### **7.4 Webhook Management**

The `libs.Webhook` library facilitates real-time event handling, such as receiving external updates or triggering bot commands.

**Example: Webhook for Payment Confirmation**:

```python
webhook_url = libs.Webhook.getUrlFor("payment_received", user_id=12345)
bot.sendMessage(f"Webhook URL: {webhook_url}")
```

***

#### **7.5 Bot.Transfer Function**

The `Bot.Transfer` function allows you to transfer a bot from one Telebot Creator account to another. It ensures that the transferred bot retains its commands, configurations, and status while validating points and ownership.

**Function Syntax**

```python
Bot.Transfer(email: str, bot_id: str, bot_token: Optional[str] = None, run_now: Optional[bool] = False) -> dict
```

**Parameters**

* **`email`**: The email address of the new owner.
* **`bot_id`**: The unique ID of the bot to be transferred.
* **`bot_token`** (optional): The Telegram Bot API token. Required if `run_now` is `True`.
* **`run_now`** (optional): If `True`, starts the bot immediately under the new owner.

**Validation and Requirements**

* The transferring user must own the bot (`bot_id`) being transferred.
* The new owner's email must exist on Telebot Creator.
* The original account must have at least 200 points to initiate the transfer.

**Usage Example**

```python
try:
    result = Bot.Transfer(
        email="<newowner>@example.com",
        bot_id="123456",
        bot_token="YOUR_BOT_API_TOKEN",
        run_now=True
    )
    bot.sendMessage(f"Bot successfully transferred to {result['bot_id']}!")
except Exception as e:
    bot.sendMessage(f"Error during transfer: {e}")
```

***

#### **7.6 Bot.info() Function**

The `Bot.info()` function retrieves detailed information about a specific bot, including its status, owner details, points, and usage statistics.

**Function Syntax**

```python
Bot.info(bot_id: Optional[str] = None, api_key: Optional[str] = None) -> dict
```

**Parameters**

* **`bot_id`** (optional): The ID of the bot to retrieve information for. If not provided, retrieves info for the current bot.
* **`api_key`** (optional): The API key of the bot owner. Used for validation if `bot_id` is provided.

**Information Returned**

* **`token`**: The bot's API token.
* **`bot_id`**: The unique ID of the bot.
* **`owner_email`**: The email of the current bot owner.
* **`status`**: The bot's current status (e.g., "Working", "Stopped").
* **`username`**: The bot's Telegram username.
* **`first_name`**: The bot's first name.
* **`account_points`**: Remaining points in the owner's account.
* **`userstat`**: Number of users interacting with the bot.

**Usage Example**

```python
try:
    details = Bot.info()
    bot.sendMessage(f"Bot Name: {details.first_name}, Status: {details.status}")
except Exception as e:
    bot.sendMessage(f"Error fetching bot info: {e}")
```

**Validation**

* Returns structured and human-readable details about the bot.

***

#### **7.7 Multi-Bot Management**

For users managing multiple bots, Telebot Creator allows seamless integration between them.

**Example: Interaction Between Two Bots**

1.  Bot A triggers Bot B via a webhook:

    ```python
    webhook_url = libs.Webhook.getUrlFor("bot_b_command", user_id=12345)
    bot.sendMessage(f"Triggering Bot B: {webhook_url}")
    ```
2.  Bot B handles the webhook and sends a response:

    ```python
    bot.sendMessage("Response from Bot B!")
    ```

***

#### **7.8 Advanced Error Handling**

Implement advanced error-handling techniques to ensure smooth workflows.

**Example**:

```python
try:
    response = libs.customHTTP().get("https://invalid.url")
    response.raise_for_status()
except Exception as e:
    bot.sendMessage(f"Error occurred: {str(e)}")
```

#### **7.9 Bot.Transfer (Expanded Use Cases)**

**1. Bot Migration Between Users**

The `Bot.Transfer` function allows seamless migration of bots from one account to another, ensuring no loss of functionality or data.

**Real-World Scenario**:

* **Use Case**: A developer transfers a bot to a business partner who will manage the bot moving forward.
*   **Example**:

    ```python
    try:
        result = Bot.Transfer(
            email="partner@example.com",
            bot_id="123456",
            bot_token="API_TOKEN",
            run_now=True
        )
        bot.sendMessage(f"Bot successfully transferred! New Bot ID: {result['bot_id']}")
    except Exception as e:
        bot.sendMessage(f"Transfer failed: {e}")
    ```

**2. Batch Transfer**

Use the `Bot.Transfer` function for multiple bots by iterating through bot IDs.

```python
bots_to_transfer = ["123456", "654321"]
for bot_id in bots_to_transfer:
    try:
        Bot.Transfer(email="newowner@example.com", bot_id=bot_id, run_now=False)
        bot.sendMessage(f"Bot ID {bot_id} transferred successfully.")
    except Exception as e:
        bot.sendMessage(f"Failed to transfer Bot ID {bot_id}: {e}")
```

***

#### **7.10 Bot.info() (Expanded Details)**

The `Bot.info()` function is a powerful tool for retrieving comprehensive bot details. It provides insight into bot usage, configurations, and ownership.

**Advanced Usage Scenarios**

1.  **Bot Status Monitoring**:

    * Periodically fetch and log bot statuses for analytics or troubleshooting.

    ```python
    details = Bot.info(bot_id="123456", api_key="VALID_API_KEY")
    bot.sendMessage(f"Bot {details.first_name} is currently {details.status}.")
    ```
2.  **Ownership Verification**:

    * Verify bot ownership before performing sensitive operations.

    ```python
    details = Bot.info(bot_id="123456", api_key="VALID_API_KEY")
    if details.owner_email == "admin@example.com":
        bot.sendMessage("Ownership verified.")
    ```
3.  **Integration with Dashboards**:

    * Fetch bot stats for visual dashboards.

    ```python
    bot_stats = Bot.info(bot_id="123456", api_key="VALID_API_KEY")
    bot.sendMessage(f"Bot Username: {bot_stats.username}, Points Remaining: {bot_stats.account_points}")
    ```

***

#### **7.11 Advanced API Integrations**

**1. Fetch and Process Data**

Integrate third-party APIs dynamically using `libs.customHTTP`.

**Example**: Fetching cryptocurrency prices.

```python
http_client = libs.customHTTP()
response = http_client.get("https://api.coindesk.com/v1/bpi/currentprice/BTC.json")
price_data = response.json()
bot.sendMessage(f"Current Bitcoin Price: {price_data['bpi']['USD']['rate']}")
http_client.close()
```

**2. Post Data to External Systems**

Use POST requests to send data to external APIs.

```python
http_client = libs.customHTTP()
response = http_client.post("https://api.example.com/submit", json={"user": "123", "action": "register"})
if response.status_code == 200:
    bot.sendMessage("Data successfully submitted!")
http_client.close()
```

**3. Automate Tasks Using Webhooks**

Automatically trigger bot actions based on webhook updates.

```python
webhook_url = libs.Webhook.getUrlFor("update_action", user_id=12345)
bot.sendMessage(f"Webhook URL for updates: {webhook_url}")
```

***

#### **7.12 Multi-Bot Management (Expanded)**

**1. Orchestrating Bots**

Enable communication between bots for advanced workflows.

* **Scenario**: Bot A collects user data and triggers Bot B to send notifications.

**Example**:

1.  **Bot A** collects data and calls Bot B's webhook:

    ```python
    webhook_url = libs.Webhook.getUrlFor(
        "notify_points",
        user_id=12345, # Don't add user_id if you don't want user specific webhook url
        bot_id="another_bot_id",
        api_key="another_bot_api_key"
    )
    bot.sendMessage(f"Webhook URL for another bot: {webhook_url}")
    ```
2.  **Bot B** processes the webhook:

    ```python
    bot.sendMessage("Notification sent via Bot B!")
    ```

**2. Delegating Tasks**

Use a primary bot to assign tasks to secondary bots.

```python
bot_ids = ["bot_123", "bot_456"]
for bot_id in bot_ids:
    Bot.runCommand(bot_id, "execute_task")
```

***

#### **7.13 Enhanced Error Handling**

**Logging Errors for Debugging**

Save errors to a persistent log for later review.

```python
try:
    risky_task()
except Exception as e:
    error_message = f"An error occurred: {str(e)}"
    Bot.saveData("last_error", error_message)
    bot.sendMessage(error_message)
```

**Custom Error Handlers**

Define custom actions for specific errors.

```python
try:
    execute_critical_task()
except ValueError as ve:
    bot.sendMessage(f"Value Error: {ve}")
except Exception as e:
    bot.sendMessage(f"Unhandled Error: {e}")
```

***

#### **7.14 Combining Features for Real-World Applications**

**Use Case: Survey Bot with API Integration**

1.  **Step 1: Collect User Input**

    ```python
    bot.sendMessage("What is your name?")
    Bot.handleNextCommand("collect_name")
    ```
2.  **Step 2: Process and Store Data**

    ```python
    name = msg
    User.saveData("name", name)
    bot.sendMessage(f"Thank you, {name}. What's your email?")
    Bot.handleNextCommand("collect_email")
    ```
3.  **Step 3: Send Data to an API**

    ```python
    email = msg
    User.saveData("email", email)
    http_client = libs.customHTTP()
    response = http_client.post(
        "https://api.example.com/register",
        json={"name": User.getData("name"), "email": email}
    )
    if response.status_code == 200:
        bot.sendMessage("Registration successful!")
    http_client.close()
    ```

**Use Case: Reward Bot with Multi-Bot Management**

1.  Bot A verifies user actions and updates points.

    ```python
    User.saveData("points", User.getData("points") + 10)
    bot.sendMessage("You've earned 10 points!")
    ```
2.  Bot B notifies the user via a secondary bot:

    ```python
    webhook_url = libs.Webhook.getUrlFor("notify_points", user_id=12345)
    bot.sendMessage(f"Notification sent via webhook: {webhook_url}")
    ```

***

####
