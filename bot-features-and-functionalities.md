# Bot Features and Functionalities

#### **6. Bot Features and Functionalities**

This section dives into the various features and functionalities available in Telebot Creator, showing how to apply them effectively in real-world scenarios. From handling user interactions to automating tasks and broadcasting messages, these features enable you to build bots that are both powerful and versatile.

***

#### **6.1 Wildcard Master Command (`*`)**

The `*` command, also known as the **Wildcard Master Command**, is triggered when a bot receives a message that does not match any predefined command. This is useful for handling fallback responses or processing unexpected user inputs.

**Use Cases**:

* Providing default responses.
* Logging unknown commands for debugging.

**Example**:

```python
bot.sendMessage("Sorry, I didn’t understand that. Type /help for a list of commands.")
```

***

#### **6.2 At Handler Command (`@`)**

The `@` command runs **before any other command** is executed. It’s primarily used for preprocessing messages, logging user activity, or setting up global conditions.

**Use Cases**:

* Validating messages before they are processed by other commands.
* Logging user activities.

**Example**:

```python
bot.sendMessage("Processing your request...")
# Continue to the next relevant command
```

***

#### **6.3 Broadcasting Messages**

The broadcasting feature allows you to send a message or execute a command for multiple users simultaneously.

**Key Functions**:

* **`broadcast`**: Sends a message or runs a command for a group of users.
* **`clearBroadcast`**: Clears records of completed broadcasts.

**Example**:

```python
bot.broadcast("Hello, everyone! This is a broadcast message.")
```

**Advanced Example with Commands**:

```python
bot.broadcast(command="promo_offer")
```

***

#### **6.4 Captcha Generation and Validation**

Telebot Creator supports automatic and manual CAPTCHA generation to ensure security during user interactions.

**Key Functions**:

* **`genCaptcha`**: Generates a CAPTCHA.
* **`validateCaptcha`**: Validates user responses to the CAPTCHA.

**Example**:

```python
captcha = bot.genCaptcha(mode="auto")
bot.sendMessage(f"Please solve this CAPTCHA: {captcha['image_url']}")
```

***

#### **6.5 Error Handling and Debugging**

Telebot Creator includes robust error-handling tools to debug and track issues in your bot.

**Features**:

* **Error Logs**: Access error logs from the dashboard under the **Errors** menu.
* **Try-Catch Blocks**: Use exception handling in TPY to manage errors gracefully.

**Example**:

```python
try:
    bot.sendMessage("Sending a risky message.")
except Exception as e:
    bot.sendMessage(f"An error occurred: {str(e)}")
```

***

#### **6.6 Persistent Data Storage**

Use `saveData` and `getData` to store and retrieve global data for your bot.

**Use Cases**:

* Tracking user progress.
* Storing settings or configurations.

**Example**:

```python
Bot.saveData("welcome_message", "Hello, welcome to our bot!")
welcome_message = Bot.getData("welcome_message")
bot.sendMessage(welcome_message)
```

***

#### **6.7 Scheduled Commands**

Schedule commands to run after a specified delay using `runCommandAfter`.

**Use Cases**:

* Sending reminders.
* Automating periodic tasks.

**Example**:

```python
bot.sendMessage("A reminder will be sent in 10 seconds.")
Bot.runCommandAfter(10, "send_reminder")
```

In the `send_reminder` command:

```python
bot.sendMessage("This is your reminder!")
```

***

#### **6.8 Webhook Integration**

Webhooks allow your bot to receive real-time updates or trigger commands based on external events.

**Key Functions**:

* **`getUrlFor`**: Generates a webhook URL for a specific command.

**Example**:

```python
webhook_url = libs.Webhook.getUrlFor("update_user_status", user_id=12345)
bot.sendMessage(f"Webhook URL: {webhook_url}")
```

***

#### **6.9 CSV File Management**

Use the `libs.CSV` library to manage data in CSV files, such as tracking user activity or creating leaderboards.

**Example**:

```python
csv = libs.CSV.CSVHandler("leaderboard.csv")
csv.create_csv(["User", "Points"])
csv.add_row({"User": "Alice", "Points": 50})
```

***

#### **6.10 Real-Time User Points**

The `left_points` global variable tracks the remaining points for executing commands.

**Example**:

```python
points = left_points
bot.sendMessage(f"You have {points} points remaining.")
```

#### **6.11 Multi-Step User Interactions**

Telebot Creator enables seamless multi-step user interactions using commands like `handleNextCommand`. This feature allows you to guide users through a sequence of questions or tasks.

**Use Cases**:

* Collecting user information step-by-step.
* Creating interactive forms or surveys.

**Example: Collecting User Details**\
Step 1: Ask for the user's name.

```python
bot.sendMessage("What is your name?")
Bot.handleNextCommand("get_name")
```

Step 2: Process the name and ask for the email.

```python
name = msg
User.saveData("name", name)
bot.sendMessage(f"Thanks, {name}! Now, what is your email?")
Bot.handleNextCommand("get_email")
```

Step 3: Process the email and confirm.

```python
email = msg
User.saveData("email", email)
bot.sendMessage("Your details have been saved. Thank you!")
```

***

#### **6.12 Dynamic Message Replies**

Telebot Creator allows bots to provide dynamic responses using variables and user-specific data.

**Use Cases**:

* Greeting users by their name.
* Sending personalized notifications.

**Example**:

```python
first_name = message.from_user.first_name
bot.sendMessage(f"Hello {first_name}, welcome back!")
```

***

#### **6.13 Inline Keyboard and Buttons**

You can create interactive inline keyboards and buttons for your bot using TPY.

**Use Cases**:

* Providing quick action buttons.
* Navigating through menus.

**Example: Inline Keyboard**

```python
keyboard = [
    [{"text": "Option 1", "callback_data": "option1"}],
    [{"text": "Option 2", "callback_data": "option2"}],
]
bot.sendMessage("Choose an option:", reply_markup={"inline_keyboard": keyboard})
```

**Handling Button Clicks**:

```python
if callback_data == "option1":
    bot.sendMessage("You selected Option 1!")
elif callback_data == "option2":
    bot.sendMessage("You selected Option 2!")
```

***

#### **6.14 Using the Random Library**

The `libs.Random` library allows you to generate random outputs for lotteries, giveaways, or dynamic bot interactions.

**Use Cases**:

* Picking random winners.
* Generating unique codes.

**Example: Generating a Random Number**

```python
random_number = libs.Random.randomInt(1, 100)
bot.sendMessage(f"Your random number is: {random_number}")
```

**Example: Generating a Random String**

```python
random_string = libs.Random.randomStr(8)
bot.sendMessage(f"Your unique code is: {random_string}")
```

***

#### **6.15 User Resource Management**

Manage user-specific resources such as points, credits, balance or quotas using the `libs.Resources` library.

**Use Cases**:

* Awarding points for user actions.
* Tracking balances for memberships or services.

**Example**:

```python
points = libs.Resources.userRes("points", user)
points.add(10)
bot.sendMessage(f"You have earned 10 points! Total points: {points.value()}")
```

***

#### **6.16 Advanced Broadcasting Options**

Broadcasting messages to users can be fine-tuned with custom commands or targeting specific groups.

**Use Cases**:

* Sending promotions to active users.
* Notifying specific users about updates.

**Advanced Example: Custom Command Broadcast**

```python
bot.broadcast(command="special_offer")
```

**Targeted Broadcasts**:

```python
users = [12345, 67890]  # List of user IDs
for user in users:
    bot.sendMessage(f"Hello, user {user}! Check out our latest offer.")
```

***

#### **6.17 Debugging Features**

Debugging is essential for ensuring your bot functions as expected. Telebot Creator provides multiple tools for error handling and tracking.

**Tools Available**:

1. **Errors Menu**:
   * Access error logs directly from the dashboard.
2. **Try-Except Handling**:
   * Use Python-style error handling to catch and manage exceptions.

**Example**:

```python
try:
    bot.sendMessage("Sending a critical message.")
except Exception as e:
    bot.sendMessage(f"An error occurred: {str(e)}")
```

***

#### **6.18 Automation with Webhooks**

Webhooks enable your bot to react to external events in real-time, such as receiving payments or triggering commands based on third-party API updates.

**Use Cases**:

* Automating responses to external triggers.
* Integrating with payment gateways or external systems.

**Example: Generating a Webhook URL**

```python
webhook_url = libs.Webhook.getUrlFor("payment_received", user_id=12345)
bot.sendMessage(f"Your webhook URL is: {webhook_url}")
```

***

#### **6.19 Managing Scheduled Tasks**

With `runCommandAfter`, you can schedule tasks to execute at a later time.

**Use Cases**:

* Sending periodic reminders.
* Automating recurring updates.

**Example**:

```python
bot.sendMessage("A reminder will be sent in 30 seconds.")
Bot.runCommandAfter(30, "reminder_task")
```

In the `reminder_task` command:

```python
bot.sendMessage("This is your reminder!")
```

***

#### **6.20 Combining Features for Complex Bots**

By combining these features, you can create highly interactive and functional bots.

**Example: Multi-Feature Use Case**

* A bot that collects user information, awards points, and broadcasts updates:

```python
bot.sendMessage("Welcome! Let’s start by getting your name.")
Bot.handleNextCommand("get_name")
```

In the `get_name` command:

```python
name = msg
User.saveData("name", name)
bot.sendMessage(f"Thanks, {name}! You’ve earned 10 points.")
points = libs.Resources.userRes("points", user)
points.add(10)
Bot.runCommandAfter(5, "send_update")
```

In the `send_update` command:

```python
bot.broadcast("Thank you for joining! Check out our updates.")
```

#### **6.21 Example Scenarios for Real-World Use Cases**

**1. Referral System**

Track and reward users for referring others to your bot.

**Workflow**:

1.  **User Shares Referral Link**:

    * Generate a unique referral link using `params` in the `/start` command.

    ```python
    pythonCopyEditbot.sendMessage(f"Invite your friends using this link: t.me/{bot.username}?start={u}")
    ```
2.  **Track Referrals**:

    * When a new user joins using the link, log the referrer.

    ```python
    pythonCopyEditreferrer = params
    if referrer:
        referrer_points = libs.Resources.userRes("points", referrer)
        referrer_points.add(10)
        bot.sendMessage(f"User {referrer} has earned 10 points!")
    ```
3.  **Reward Top Referrers**:

    * Use `libs.Resources.userRes` to fetch the top contributors.

    ```python
    pythonCopyEdittop_referrers = libs.Resources.userRes("points").getAllData(5)
    bot.sendMessage(f"Top referrers: {top_referrers}")
    ```

***

**2. Event Reminder Bot**

Let users set reminders for specific dates and times.

**Workflow**:

1.  **Collect Event Details**:

    ```python
    bot.sendMessage("What is the event name?")
    Bot.handleNextCommand("get_event_name")
    ```
2.  **Ask for the Date and Time**:

    ```python
    event_name = msg
    User.saveData("event_name", event_name)
    bot.sendMessage("When is the event? (Format: YYYY-MM-DD HH:MM)")
    Bot.handleNextCommand("get_event_time")
    ```
3.  **Schedule the Reminder**:

    ```python
    event_time = msg  # Save and validate date-time
    Bot.runCommandAfter(seconds_until_event, "send_event_reminder")
    bot.sendMessage("Reminder scheduled!")
    ```
4.  **Send the Reminder**:

    ```python
    event_name = User.getData("event_name")
    bot.sendMessage(f"Reminder: The event '{event_name}' is happening now!")
    ```

***

#### **6.22 Integration Highlights**

**Fetching External Data with `libs.customHTTP`**

Integrate your bot with external APIs to provide dynamic content.

**Example: Fetching Weather Data**

```python
http_client = libs.customHTTP()
response = http_client.get("https://api.weatherapi.com/v1/current.json?key=API_KEY&q=London")
weather_data = response.json()
bot.sendMessage(f"Current temperature in London: {weather_data['current']['temp_c']}°C")
http_client.close()
```

***

#### **6.24 Feature Customization Options**

**Custom Inline Keyboards**

Create dynamic options for users.

```python
keyboard = [
    [{"text": "Buy Now", "url": "https://example.com"}],
    [{"text": "Contact Support", "callback_data": "contact_support"}],
]
bot.sendMessage("Choose an action:", reply_markup={"inline_keyboard": keyboard})
```

***

#### **6.25 Common Mistakes and Solutions**

| **Mistake**                            | **Solution**                                                           |
| -------------------------------------- | ---------------------------------------------------------------------- |
| Incorrectly scheduling commands.       | Ensure the time delay for `runCommandAfter` is accurate and positive.  |
| Resource mismanagement (e.g., points). | Use `libs.Resources` consistently to avoid overwriting or losing data. |
| Webhook not triggering.                | Verify the webhook URL and ensure the command exists in your bot.      |

***

#### **6.26 Debugging with Logs**

**Access Logs via the Dashboard**:

* Use the **Errors** menu to view logs for failed commands or system issues.

**Inline Logging**:

```python
try:
    bot.sendMessage("Processing your request...")
except Exception as e:
    bot.sendMessage(f"Error: {e}") # or use 
    Bot.saveData("last_error", str(e))
```
