# Broadcast Function In TBC

#### **Broadcast Function Documentation**

The `broadcast` function in Telebot Creator allows you to send messages or execute commands for multiple users efficiently. It supports both direct messaging with pre-defined functions and executing custom code for dynamic broadcasts.

***

#### **Function Syntax**

```python
Bot.broadcast(
    code=None, 
    command=None, 
    callback_url=None, 
    bot_id=None, 
    api_key=None, 
    function=None, 
    warnings=None, 
    **kwargs
)
```

***

#### **Parameters**

1. **`code`** (_Optional_):
   * Custom TPY code to execute for the broadcast.
   * Example: A piece of code that sends personalized messages.
2. **`command`** (_Optional_):
   * The name of a pre-defined command in the bot to execute during the broadcast.
   * If `code` is not provided, `command` must be specified.
3. **`callback_url`** (_Optional_):
   * URL to be called after the broadcast task is executed for each user.
4. **`bot_id`** (_Optional_):
   * The ID of the bot for which the broadcast is being created.
   * **Requires** `api_key` for validation if specified.
5. **`api_key`** (_Optional_):
   * API key of the bot owner. Used to validate access when `bot_id` is provided.
6. **`function`** (_Optional_):
   * Pre-defined Telegram functions for broadcasting (e.g., `send_message`, `send_photo`).
   * Full list of allowed functions:
     * `send_message`, `send_photo`, `send_video`, `send_animation`
     * `send_audio`, `send_document`, `forward_message`
     * `send_sticker`, `send_poll`, `send_location`, etc.
7. **`warnings`** (_Optional_):
   * Whether to display warnings during test execution (`True` by default).
8. **`**kwargs`** (_Optional_):
   * Additional arguments for the selected `function`.

***

#### **Validation and Limitations**

* **Maximum Global Broadcasts**: 1000 simultaneous broadcasts across all bots.
* **User Broadcast Limit**: 2 running broadcasts per bot.
* **Command Validation**: The `command` provided must exist in the bot.
* **Function Restrictions**: Only allowed functions can be used for broadcasting.

If these limits are exceeded, the function returns an error message.

***

#### **Usage Examples**

**1. Broadcasting a Message**

Broadcast a message to all users using the `send_message` function:

```python
Bot.broadcast(
    function="send_message",
    text="Hello, everyone! Check out our new feature!"
)
```

**2. Broadcasting a Command**

Execute a pre-defined command across all users:

```python
Bot.broadcast(
    command="promo_offer"
)
```

**3. Broadcasting with Custom Code**

Use TPY code for a dynamic broadcast:

```python
Bot.broadcast(
    code="""
    first_name = message.from_user.first_name
    bot.sendMessage(f"Hello {first_name}, don’t miss our latest update!")
    """
)
```

**4. Broadcasting for Another Bot**

Broadcast messages for a different bot by providing `bot_id` and `api_key`:

```python
Bot.broadcast(
    bot_id="another_bot_id",
    api_key="another_bot_api_key",
    function="send_message",
    text="Greetings from Bot B!"
)
```

**5. Broadcasting with a Callback URL**

Trigger a callback URL after the broadcast execution:

```python
Bot.broadcast(
    function="send_message",
    text="Hello! Thank you for being with us.",
    callback_url="https://example.com/broadcast-callback"
)
```

***

#### **Error Handling**

**Common Errors**

1. **Exceeding Limits**:
   * Error: "You already have 2 running broadcasts."
   * Solution: Wait for an existing broadcast to complete or stop it manually.
2. **Invalid Command**:
   * Error: "Command doesn’t exist."
   * Solution: Ensure the `command` specified is defined in your bot.
3. **Invalid Function**:
   * Error: "Function `<name>` not allowed for broadcasting."
   * Solution: Use only allowed functions for the broadcast.
4. **API Key Validation**:
   * Error: "API key not valid."
   * Solution: Verify the `bot_id` and `api_key` combination.

***

#### **Best Practices**

1. **Efficient Broadcasting**:
   * Use pre-defined commands or reusable functions to minimize errors.
   * Avoid overloading the system with frequent or unnecessary broadcasts.
2. **Test Before Deployment**:
   * Use the test run feature built into the `broadcast` function to validate your setup before executing the full broadcast.
3. **Secure API Keys**:
   * Never expose `api_key` in public code or logs.
4. **Monitor Broadcast Status**:
   * Use the `broadstatus` collection to track running broadcasts and their success rate.

***

This documentation provides a comprehensive guide to using the `broadcast` function for efficient and scalable messaging in Telebot Creator.
