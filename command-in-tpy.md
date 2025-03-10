# Command in TPY

#### **3. Commands in TPY**

Commands are the backbone of every bot built on Telebot Creator. They define how a bot responds to specific inputs from users, such as messages or commands like `/start` or `/help`. This section explains what commands are, how to write them using **TPY (Telebot Python)**, and advanced techniques for chaining commands, handling user interactions, and adding interactivity.

***

**3.1 What Are Commands?**

* A **command** is a predefined trigger in your bot that responds to specific user messages. For example:
  * `/start`: Greets the user and provides an introduction.
  * `/help`: Displays a list of available commands and their usage.
* Commands can perform actions like:
  * Sending messages or media.
  * Handling user inputs dynamically.
  * Running scheduled tasks or interacting with APIs.

***

**3.2 Writing Commands in TPY**

TPY (Telebot Python) is a customized, lightweight version of Python designed for Telebot Creator. It simplifies the process of writing commands while providing powerful tools for bot development.

**Example of a Basic Command**

Here’s how you create a `/start` command that sends a welcome message:

```python
bot.sendMessage("Welcome to my bot! Use /help to see available commands.")
```

***

**3.3 Using Variables and Parameters**

Variables in TPY allow you to customize responses and interact dynamically with users.

**Accessing User Information**

You can use variables like `message.from_user.first_name` to personalize your messages. For example:

```python
first_name = message.from_user.first_name
bot.sendMessage(f"Hello {first_name}, welcome to my bot!")
```

**Handling Command Parameters**

Commands can accept parameters passed by users. For example, in `/start 12345`, the parameter `12345` can be accessed as `params`:

```python
refer_id = params
bot.sendMessage(f"You were referred by ID: {refer_id}.")
```

***

**3.4 Handling User Interactions**

TPY provides several methods to manage and guide user interactions effectively.

**1. `handleNextCommand`**

This method waits for the user’s next input and routes it to a specific command.

**Example**:

```python
bot.sendMessage("Please enter your email:")
handleNextCommand("process_email")
```

In the `process_email` command:

```python
email = msg
bot.sendMessage(f"Thank you! We received your email: {email}.")
```

**2. `runCommand`**

This method allows you to trigger another command immediately.

**Example**:

```python
bot.sendMessage("Redirecting you to the /help command...")
bot.runCommand("help")
```

**3. `runCommandAfter`**

This schedules a command to execute after a specified delay (in seconds).

**Example**:

```python
bot.sendMessage("You will receive a message in 5 seconds.")
bot.runCommandAfter(5, "delayed_message")
```

In the `delayed_message` command:

```python
bot.sendMessage("This is the delayed message!")
```

***

**3.5 Advanced Command Techniques**

**Wildcard Master Command (`*`)**

The wildcard (`*`) command captures any input that doesn’t match a predefined command. This is useful for creating fallback responses.

**Example**:

```python
bot.sendMessage("Sorry, I didn’t understand that. Type /help for a list of commands.")
```

**At Handler Command (`@`)**

The at handler (`@`) command executes before any other command. Use it for preprocessing messages or logging user activity.

**Example**:

```python
bot.sendMessage("Processing your request...")
# Continue to other commands
```

***

**3.6 Examples of Common Commands**

**1. Greet Users**

```python
bot.sendMessage("Welcome! Use /help to get started.")
```

**2. Display Help Menu**

```python
bot.sendMessage("""
Here are the available commands:
/start - Start the bot
/help - Show this help menu
/points - Check your current points
""")
```

**3. Check User Points**

```python
points = left_points
bot.sendMessage(f"You have {points} points remaining in your account.")
```

**4. Collect User Input**

```python
bot.sendMessage("What’s your favorite color?")
bot.handleNextCommand("save_color")
```

In the `save_color` command:

```python
color = msg
User.saveData("favorite_color", color)
bot.sendMessage(f"Got it! Your favorite color is {color}.")
```

***

**3.7 Chaining Commands**

Commands can be chained together to create complex workflows. For example, a multi-step form:

1.  Ask for the user’s name:

    ```python
    bot.sendMessage("What’s your name?")
    handleNextCommand("get_name")
    ```
2.  Process the name and ask for the email:

    ```python
    name = msg
    User.saveData("name", name)
    bot.sendMessage(f"Hi {name}! Now, what’s your email?")
    handleNextCommand("get_email")
    ```
3.  Process the email and confirm:

    ```python
    email = msg
    User.saveData("email", email)
    bot.sendMessage("Thank you! Your details have been saved.")
    ```

***

#### **Summary**

Commands in TPY are the heart of every bot on Telebot Creator. By mastering command creation, parameter handling, and advanced techniques like chaining and scheduling, you can build bots that are interactive, intelligent, and highly functional.
