# Tips, Best Practices, and Troubleshooting

#### **9. Tips, Best Practices, and Troubleshooting**

This section provides essential guidance for optimizing bot performance, managing resources effectively, ensuring security, and troubleshooting common issues in Telebot Creator. By following these practices, you can create highly reliable, efficient, and secure bots.

***

### **9.1 Tips for Optimizing Your Bot**

#### **Efficient Point Usage**

1.  **Combine Actions into a Single Command**:

    * Reduce redundant commands by combining related actions.

    ```python
    bot.sendMessage("Welcome!")
    bot.sendMessage("Use /help for instructions.")
    ```

    **Combine into one:**

    ```python
    bot.sendMessage("Welcome! Use /help for instructions.")
    ```
2. **Use Targeted Broadcasts**:
   * Use `Bot.broadcast()` to reach your audience efficiently instead of manual loops.
3.  **Schedule Tasks Appropriately**:

    * Use `runCommandAfter` for periodic tasks to avoid invoking commands unnecessarily.

    ```python
    Bot.runCommandAfter(3600, "send_reminder")  # Execute a reminder after 1 hour
    ```
    
    > **New in 4.9.0**: You can now schedule tasks up to 1 year (365 days) ahead, with a minimum interval of 0.1 seconds. Maximum scheduled tasks per user increased from 20 to 100.
    
4. **Minimize Repeated API Calls**:
   * Cache data that doesn't change frequently (e.g., user statistics or configuration).
   
   > **New in 4.9.0**: Use standard `HTTP` module instead of `libs.customHTTP()` for better performance and reliability.

***

#### **Enhancing Performance**

1. **Handle Large User Bases**:
   * Use asynchronous operations and efficient workflows to manage broadcasts or commands.
   * Limit unnecessary broadcasts to inactive users.
2. **Optimize User Interactions**:
   * Use `handleNextCommand` to guide users through workflows instead of using multiple commands.
3. **Efficient Error Handling**:
   *   Log and analyze errors for better debugging:

       ```python
       try:
           bot.sendMessage("Attempting an action...")
       except Exception as e:
           Bot.saveData("last_error", str(e))
           bot.sendMessage(f"Error occurred: {e}")
       ```

***

#### **Code Execution Limits**

> **New in 4.9.0**:
> 
> 1. Code execution timeout has been extended from 60 to 120 seconds.
> 2. A new `time.sleep()` function is available with a maximum limit of 10 seconds.
> 3. For ultra-fast commands (under 0.4 seconds), a rate limit of 5 executions within 5 seconds is enforced to prevent abuse.
> 4. Do not use `import x` statements in your code. Use the built-in libraries instead.
> 5. For handling inline queries and other update types, use the `/handler_<update_type>` command format.

***

#### **Improving Security**

1. **Secure Sensitive Data**:
   * Encrypt user data (e.g., emails or payment information) before saving it.
2. **Dynamic Webhook Generation**:
   *   Generate secure webhook URLs with `libs.Webhook.getUrlFor` and validate inputs:

       ```python
       webhook_url = libs.Webhook.getUrlFor("process_data", user_id=12345)
       ```
3. **Restrict API Access**:
   * Use API keys for authentication when interacting with external systems.
4. **Data Minimization**:
   * Store only the data you need to avoid unnecessary risk.

***

### **9.2 Best Practices**

#### **Developing Reliable Workflows**

1.  **Use Multi-Step Commands**:

    * Break complex processes into smaller steps using `handleNextCommand`.

    ```python
    bot.sendMessage("What's your name?")
    Bot.handleNextCommand("get_name")
    ```
2.  **Validate Inputs**:

    * Always validate user inputs to prevent errors or misuse.

    ```python
    if not params.isnumeric():
        bot.sendMessage("Invalid input. Please enter a valid number.")
    ```

***

#### **Optimizing Bot Features**

1. **Leverage Built-In Libraries**:
   * Use libraries like `libs.CSV` for data management or `libs.Random` for randomization.
2.  **Monitor Points Usage**:

    * Use `Bot.info()` to track points and optimize commands accordingly.

    ```python
    details = Bot.info(bot_id="123456")
    bot.sendMessage(f"Points left: {details.account_points}")
    ```
3.  **Use Test Runs for Broadcasts**:

    * Validate your broadcast before deploying to avoid errors or excessive point usage.

    ```python
    Bot.broadcast(
        function="send_message",
        text="Testing the broadcast system."
    )
    ```

***

### **9.3 Troubleshooting Common Issues**

#### **Command Errors**

* **Issue**: The bot doesn't respond to a command.
* **Causes**:
  * Command is misspelled or case-sensitive mismatch.
  * Missing parameters in the command.
* **Solution**:
  * Verify the command exists and is spelled correctly.
  * Ensure required parameters are passed.

***

#### **Broadcast Failures**

* **Issue**: Broadcast does not execute or returns an error.
* **Causes**:
  * Too many running broadcasts (limit: 2 per bot, 1000 globally).
  * Invalid or unsupported `function`.
* **Solution**:
  *   Check broadcast limits:

      ```python
      Bot.broadcast(
          function="send_message",
          text="Broadcast Test Message"
      )
      ```
  * Use only supported functions for broadcasting.

***

#### **Webhook Issues**

* **Issue**: Webhook does not trigger the intended command.
* **Causes**:
  * Incorrect webhook URL.
  * Command does not exist in the bot.
* **Solution**:
  *   Validate webhook URL generation:

      ```python
      webhook_url = libs.Webhook.getUrlFor("update_event", user_id=12345)
      bot.sendMessage(f"Webhook URL: {webhook_url}")
      ```
  * Ensure the command is properly defined in the bot.

***

#### **Payment Errors**

* **Issue**: Payments fail or do not register.
* **Causes**:
  * Incorrect API keys or misconfigured payment gateway.
  * Network issues between bot and payment service.
* **Solution**:
  *   Verify API keys and gateway settings:

      ```python
      libs.Coinbase.setKeys("API_KEY", "SECRET")
      client = libs.Coinbase.post()
      ```

***

#### **Bot Transfer Issues**

* **Issue**: Bot transfer fails.
* **Causes**:
  * Insufficient points (minimum 200 required).
  * Invalid `bot_id` or `api_key`.
* **Solution**:
  *   Check points using `Bot.info()`:

      ```python
      details = Bot.info(bot_id="123456")
      bot.sendMessage(f"Points available: {details.account_points}")
      ```

***

#### **General Debugging Tips**

1. **Log Errors**:
   *   Use `Bot.saveData()` to log errors for analysis:

       ```python
       try:
           risky_task()
       except Exception as e:
           Bot.saveData("last_error", str(e))
           bot.sendMessage(f"Error: {e}")
       ```
2. **Verify Configurations**:
   * Double-check settings for commands, libraries, and webhooks.

***

### **9.4 Advanced Tips**

#### **Monitor Bot Performance**

*   Use `Bot.info()` to retrieve metrics like status, user engagement, and remaining points:

    ```python
    details = Bot.info(bot_id="123456")
    bot.sendMessage(f"Bot Status: {details.status}, Points Left: {details.account_points}")
    ```

***

#### **Scale with Multi-Bot Management**

*   Enable communication between multiple bots using webhooks:

    ```python
    webhook_url = libs.Webhook.getUrlFor(
        command="notify_event",
        bot_id="another_bot_id",
        api_key="another_bot_api_key"
    )
    bot.sendMessage(f"Webhook for another bot: {webhook_url}")
    ```

***

#### **Secure API Keys and Tokens**

* Regularly rotate API keys and ensure they are not exposed in public logs or repositories

#### **9.5 Advanced Use Cases for Tips and Best Practices**

**1. Efficient Data Management with CSV**

Telebot Creator's `libs.CSV` library allows you to handle large datasets effectively. Use this for leaderboards, attendance tracking, or survey results.

**Example: Create and Update a Leaderboard**

```python
csv_handler = libs.CSV.CSVHandler("leaderboard.csv")
csv_handler.create_csv(["Name", "Points"])

# Add or update a user's points
csv_handler.add_row({"Name": "Alice", "Points": 100})
csv_handler.edit_row(0, {"Name": "Alice", "Points": 150})  # Update Alice's points

leaderboard = csv_handler.get()
bot.sendMessage(f"Leaderboard: {leaderboard}")
```

***

**2. Payment Handling with Coinbase**

Efficiently handle payments using `libs.Coinbase`. Automate user interactions based on payment status.

**Example: Automate Subscription Payments**

```python
libs.Coinbase.setKeys("API_KEY", "SECRET")
client = libs.Coinbase.post()

# Create a payment request
payment = client.createCharge({
    "name": "Subscription",
    "description": "Monthly Subscription",
    "local_price": {"amount": "10.00", "currency": "USD"},
    "pricing_type": "fixed_price"
})

bot.sendMessage(f"Pay here: {payment['hosted_url']}")

# Check payment status
charge_id = payment["id"]
status = client.retrieveCharge(charge_id)["status"]

if status == "CONFIRMED":
    bot.sendMessage("Thank you for your payment!")
else:
    bot.sendMessage(f"Payment is still pending. Status: {status}")
```

***

**3. Dynamic Webhook Management**

Using `libs.Webhook.getUrlFor`, you can dynamically create webhooks for real-time event handling.

**Example: Notify Users on External Events**

```python
webhook_url = libs.Webhook.getUrlFor(
    command="notify_user",
    user_id=12345
)
bot.sendMessage(f"Webhook URL: {webhook_url}")
```

**Handle Notifications in the `notify_user` Command**:

```python
bot.sendMessage("You've received a new notification!")
```

***

**4. Combining Multi-Step Interactions with Reminders**

Create workflows that interact with users and automate follow-ups.

**Example: Survey with Reminders**

1.  Collect user input:

    ```python
    bot.sendMessage("What's your favorite color?")
    Bot.handleNextCommand("save_color")
    ```
2.  Save the input and set a reminder:

    ```python
    color = msg
    User.saveData("favorite_color", color)
    bot.sendMessage(f"Got it! Your favorite color is {color}.")
    Bot.runCommandAfter(3600, "send_reminder")
    ```
3.  Send the reminder:

    ```python
    bot.sendMessage("Don't forget to tell your friends about our bot!")
    ```

***

#### **9.6 Advanced Debugging Techniques**

**1. Track Errors Over Time**

Store error logs with timestamps for later analysis:

```python
try:
    risky_action()
except Exception as e:
    bot.sendMessage(f"An error occurred: {e}")
```

Retrieve and review logs:

```python
logs = Bot.getData("error_log")
bot.sendMessage(f"Error Logs:\n{logs}")
```

***

**2. Use Test Runs for Broadcasts**

Before sending a broadcast to all users, validate it with a test run:

```python
Bot.broadcast(
    function="send_message",
    text="Testing broadcast system."
)
```

***

#### **9.7 Real-World Scenarios for Best Practices**

**Scenario 1: Handling a Viral Campaign**

When a bot receives an influx of users due to a campaign:

* Use caching for common responses to reduce API calls.
* Implement a queue system for processing tasks like rewards or verifications.

**Scenario 2: Managing High Broadcast Demand**

If multiple broadcasts are required:

* Use `Bot.broadcast()` with precise targeting to avoid exceeding limits.
*   Monitor broadcasts with:

    ```python
    status = self.db['broadstatus'].find({"status": "running"})
    bot.sendMessage(f"Current running broadcasts: {len(list(status))}")
    ```

**Scenario 3: Secure Payment Bot**

* Rotate API keys regularly.
*   Use `callback_url` to handle payment confirmations securely:

    ```python
    Bot.broadcast(
        function="send_message",
        text="Your subscription has been activated!",
        callback_url="https://example.com/payment-callback"
    )
    ```

***

#### **9.8 Frequently Asked Questions (FAQs)**

**1. Why isn't my command executing?**

* **Check**: Ensure the command exists and matches the trigger exactly.
* **Fix**: Verify case sensitivity and parameter requirements.

**2. What should I do if a webhook fails?**

* **Check**: Confirm the webhook URL and command are valid.
*   **Fix**: Regenerate the webhook with:

    ```python
    libs.Webhook.getUrlFor(command="my_command", user_id=12345)
    ```

**3. Why are broadcasts limited?**

* **Reason**: Each bot is limited to 2 broadcasts to ensure system stability.
* **Fix**: Wait for existing broadcasts to complete or optimize the broadcast content.

**4. How do I secure sensitive data?**

* **Best Practices**:
  * Encrypt data before storage.
  * Restrict access to commands handling sensitive data.
