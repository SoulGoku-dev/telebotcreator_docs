# Version 4.8.0 Update

## New Features Overview

The version 4.8.0 update introduces several powerful enhancements to improve your bot development experience:

1. **New Account Class**: Direct access to account-level operations through the globally accessible `Account` variable.
2. **Enhanced Resource Management**: New `accountRes` class for managing account-level resources.
3. **Improved Server Stability**: Enhanced server maintenance and durability.
4. **Command Aliases**: Support for command aliases in the upcoming UI update.
5. **Bot Recovery System**: Ability to recover deleted bots within 90 days.

## New Account Class

The 4.8.0 update introduces the powerful `Account` class, giving developers direct access to account-level operations. This class allows for comprehensive management of bots, commands, statistics, and more from a centralized interface.

### Accessing the Account Class

The Account class is globally accessible in your bot code through the `Account` variable, similar to how you access the `Bot` and `User` classes:

```python
# The Account variable is directly available in your bot code
result = Account.get_bots_list()
if result["ok"]:
    for bot_info in result["result"]:
        Bot.sendMessage(f"Bot: {bot_info['name']}")
```

No initialization is needed as the variable is automatically created with the correct authentication and database connections.

### Account Data Management Methods

#### saveData

Stores data at the account level, accessible across all bots.

```python
Account.saveData(name, data)
```

**Parameters:**
- `name` (Required): Name identifier for the data.
- `data` (Required): The data to store (limited to 10MB).

**Example:**
```python
result = Account.saveData("global_settings", {"theme": "dark", "notifications": True})
Bot.sendMessage(f"Save result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": "true",
  "result": "Added new data"
}
```

#### getData

Retrieves previously stored account data.

```python
Account.getData(name)
```

**Parameters:**
- `name` (Required): Name of the data to retrieve.

**Example:**
```python
settings = Account.getData("global_settings")
if settings:
    Bot.sendMessage(f"Theme: {settings['theme']}")
else:
    Bot.sendMessage("No settings found")
```

**Example Output:**
```json
{
  "theme": "dark",
  "notifications": true
}
```

#### deleteData

Deletes account data by name.

```python
Account.deleteData(name)
```

**Parameters:**
- `name` (Required): Name of the data to delete.

**Example:**
```python
result = Account.deleteData("temp_data")
Bot.sendMessage(f"Delete result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": "true",
  "result": "deleted"
}
```

#### getDataFile

Returns account data as a file that can be sent to users.

```python
Account.getDataFile(name, output_format="txt")
```

**Parameters:**
- `name` (Required): Name of the data to retrieve.
- `output_format` (Optional): Format of the output file (currently supports "txt").

**Example:**
```python
try:
    file = Account.getDataFile("report_data")
    Bot.sendDocument(file)
except ValueError as e:
    Bot.sendMessage(f"Error: {str(e)}")
```

**Example Output:**
```
# Returns a file-like object that can be directly passed to Bot.sendDocument()
# The file contains the stored data in text format
```

#### getAllData

Retrieves all account data entries, optionally filtered by name pattern.

```python
Account.getAllData(name=None, output_format="json")
```

**Parameters:**
- `name` (Optional): Name pattern to filter data.
- `output_format` (Optional): Format of the output file (currently supports "json").

**Example:**
```python
data_file = Account.getAllData("config_")
Bot.sendDocument(data_file)
```

**Example Output:**
```
# Returns a file-like object containing JSON data in the format:
# [
#   {
#     "name": "config_user",
#     "data": {"setting1": "value1", "setting2": "value2"},
#     "time": "2023-05-15 14:30:22"
#   },
#   {
#     "name": "config_bot",
#     "data": {"timeout": 30, "retry": true},
#     "time": "2023-05-16 09:15:43"
#   }
# ]
```

#### deleteAllData

Deletes all account data, with optional exclusions and bot data inclusion.

```python
Account.deleteAllData(except_data=None, include_bot_data=False)
```

**Parameters:**
- `except_data` (Optional): List of data names to preserve.
- `include_bot_data` (Optional): Whether to also delete bot-level data.

**Example:**
```python
result = Account.deleteAllData(except_data=["important_settings"], include_bot_data=True)
Bot.sendMessage(f"Data cleared: {result['result']}")
```

**Example Output:**
```json
{
  "ok": "true",
  "result": "deleted"
}
```

#### info

Returns basic account information.

```python
Account.info()
```

**Example:**
```python
info = Account.info()
Bot.sendMessage(f"Account plan: {info.plan}, Points left: {info.points_left}")
```

**Example Output:**
```json
{
  "email": "user@example.com",
  "plan": "Premium",
  "points_resetAt": "2023-06-01 00:00:00",
  "points_left": 8500,
  "ep": 1000
}
```

### Bot Management Methods

#### start_bot

Starts a bot by setting its webhook.

```python
Account.start_bot(botid)
```

**Parameters:**
- `botid` (Required): ID of the bot to start.

**Example:**
```python
result = Account.start_bot("1234567")
Bot.sendMessage(f"Start result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Bot started successfully"
}
```

#### stop_bot

Stops a bot by removing its webhook.

```python
Account.stop_bot(botid)
```

**Parameters:**
- `botid` (Required): ID of the bot to stop.

**Example:**
```python
result = Account.stop_bot("1234567")
Bot.sendMessage(f"Stop result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Bot stopped successfully"
}
```

#### restart_bot

Restarts a bot by stopping and then starting it.

```python
Account.restart_bot(botid)
```

**Parameters:**
- `botid` (Required): ID of the bot to restart.

**Example:**
```python
result = Account.restart_bot("1234567")
Bot.sendMessage(f"Restart result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Bot started successfully"
}
```

#### create_bot

Creates a new bot with the given token.

```python
Account.create_bot(bot_token, bot_name=None, bot_username=None)
```

**Parameters:**
- `bot_token` (Required): Telegram bot token.
- `bot_name` (Optional): Name for the bot (retrieved from Telegram if not provided).
- `bot_username` (Optional): Username for the bot (retrieved from Telegram if not provided).

**Example:**
```python
result = Account.create_bot("123456789:ABCDEF-ghijklmnopqrstuvwxyz")
if result["ok"]:
    Bot.sendMessage(f"Created bot with ID: {result['botid']}")
else:
    Bot.sendMessage(f"Error: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Bot created successfully",
  "botid": "7654321"
}
```

#### delete_bot

Deletes a bot (temporarily or permanently).

```python
Account.delete_bot(botid, permanent=False)
```

**Parameters:**
- `botid` (Required): ID of the bot to delete.
- `permanent` (Optional): Whether to permanently delete or keep for recovery.

**Example:**
```python
result = Account.delete_bot("1234567")
Bot.sendMessage(f"Delete result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Bot deleted successfully"
}
```

#### recover_bot

Recovers a previously deleted bot.

```python
Account.recover_bot(botid, new_token=None)
```

**Parameters:**
- `botid` (Required): ID of the bot to recover.
- `new_token` (Optional): New token if the original is no longer valid.

**Example:**
```python
result = Account.recover_bot("1234567")
Bot.sendMessage(f"Recovery result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Bot recovered successfully"
}
```

#### get_deleted_bots

Retrieves a list of deleted bots that can be recovered.

```python
Account.get_deleted_bots()
```

**Example:**
```python
bots = Account.get_deleted_bots()
if bots["ok"] and bots["result"]:
    for bot_info in bots["result"]:
        Bot.sendMessage(f"Bot {bot_info['name']} - Days remaining: {bot_info['days_remaining']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": [
    {
      "botid": "1234567",
      "name": "Customer Support Bot",
      "username": "customer_support_bot",
      "deleted_at": "2023-05-01T14:30:22Z",
      "commands_count": 15,
      "days_remaining": 60,
      "recoverable": true
    },
    {
      "botid": "7654321",
      "name": "Quiz Bot",
      "username": "quiz_master_bot",
      "deleted_at": "2023-04-15T09:12:45Z",
      "commands_count": 8,
      "days_remaining": 44,
      "recoverable": true
    }
  ]
}
```

#### get_deleted_bots_stats

Provides statistics about deleted bots, including counts and expiration information.

```python
Account.get_deleted_bots_stats()
```

**Example:**
```python
stats = Account.get_deleted_bots_stats()
if stats["ok"]:
    result = stats["result"]
    Bot.sendMessage(f"Total deleted: {result['total']}, Recoverable: {result['recoverable']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": {
    "total": 5,
    "recoverable": 3,
    "expired": 2,
    "expiring_soon": [
      {
        "botid": "2345678",
        "name": "Test Bot",
        "days_remaining": 15
      }
    ]
  }
}
```

#### permanent_delete_bot

Permanently removes a deleted bot from the recovery system.

```python
Account.permanent_delete_bot(botid)
```

**Parameters:**
- `botid` (Required): ID of the deleted bot to permanently remove.

**Example:**
```python
result = Account.permanent_delete_bot("1234567")
Bot.sendMessage(f"Permanent deletion result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Bot permanently deleted"
}
```

#### clear_expired_bots

Admin-only method to clear expired bots (deleted over 90 days ago).

```python
Account.clear_expired_bots()
```

**Example:**
```python
result = Account.clear_expired_bots()
Bot.sendMessage(f"Cleared expired bots: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Cleared 3 expired bots"
}
```

#### clone_bot

Creates a clone of an existing bot.

```python
Account.clone_bot(botid, new_token=None)
```

**Parameters:**
- `botid` (Required): ID of the bot to clone.
- `new_token` (Optional): Token for the new bot.

**Example:**
```python
result = Account.clone_bot("1234567", "987654321:ABCDEF-ghijklmnopqrstuvwxyz")
if result["ok"]:
    Bot.sendMessage(f"Created clone with ID: {result['botid']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Bot cloned successfully",
  "botid": "8765432"
}
```

#### get_bots_list

Retrieves a list of all bots in the account.

```python
Account.get_bots_list()
```

**Example:**
```python
bots = Account.get_bots_list()
if bots["ok"] and bots["result"]:
    for bot_info in bots["result"]:
        Bot.sendMessage(f"Bot {bot_info['name']} - Status: {bot_info['status']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": [
    {
      "botid": "1234567",
      "name": "Support Bot",
      "username": "support_bot",
      "status": "working",
      "creation_date": "14:30:22 01:05:2023",
      "has_token": true
    },
    {
      "botid": "7654321",
      "name": "Quiz Bot",
      "username": "quiz_master_bot",
      "status": "stopped",
      "creation_date": "09:15:43 16:04:2023",
      "has_token": true
    }
  ]
}
```

#### get_bot_info

Retrieves detailed information about a specific bot.

```python
Account.get_bot_info(botid)
```

**Parameters:**
- `botid` (Required): ID of the bot to get information for.

**Example:**
```python
info = Account.get_bot_info("1234567")
if info["ok"]:
    bot_info = info["result"]
    Bot.sendMessage(f"Bot {bot_info['name']}: {bot_info['user_count']} users, {bot_info['command_count']} commands")
```

**Example Output:**
```json
{
  "ok": true,
  "result": {
    "botid": "1234567",
    "name": "Support Bot",
    "username": "support_bot",
    "status": "working",
    "creation_date": "14:30:22 01:05:2023",
    "has_token": true,
    "command_count": 12,
    "user_count": 278,
    "points_used": 5432
  }
}
```

#### get_bot_status

Checks the status of a bot.

```python
Account.get_bot_status(botid)
```

**Parameters:**
- `botid` (Required): ID of the bot to check.

**Example:**
```python
status = Account.get_bot_status("1234567")
if status["ok"]:
    Bot.sendMessage(f"Bot status: {status['result']['status']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": {
    "status": "online"
  }
}
```

#### get_bot_data

Retrieves stored global data for a specific bot.

```python
Account.get_bot_data(botid, name)
```

**Parameters:**
- `botid` (Required): ID of the bot to get data for.
- `name` (Required): Name of the data to retrieve.

**Example:**
```python
data = Account.get_bot_data("1234567", "bot_settings")
if data["ok"]:
    Bot.sendMessage(f"Bot settings: {data['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": {
    "welcome_msg": "Hello!",
    "timeout": 30,
    "language": "en"
  }
}
```

#### set_bot_data

Stores global data for a specific bot.

```python
Account.set_bot_data(botid, name, data)
```

**Parameters:**
- `botid` (Required): ID of the bot to store data for.
- `name` (Required): Name identifier for the data.
- `data` (Required): The data to store (limited to 10MB).

**Example:**
```python
result = Account.set_bot_data("1234567", "bot_settings", {"welcome_msg": "Hello!"})
Bot.sendMessage(f"Save result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Data saved successfully"
}
```

### Command Management Methods

#### create_command

Creates a new command for a bot.

```python
Account.create_command(botid, command, code)
```

**Parameters:**
- `botid` (Required): ID of the bot to create the command for.
- `command` (Required): Name of the command.
- `code` (Required): Code for the command.

**Example:**
```python
result = Account.create_command("1234567", "/hello", "Bot.sendMessage('Hello, world!')")
Bot.sendMessage(f"Command creation result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Command created successfully"
}
```

#### delete_command

Deletes a command from a bot.

```python
Account.delete_command(botid, command)
```

**Parameters:**
- `botid` (Required): ID of the bot to delete the command from.
- `command` (Required): Name of the command to delete.

**Example:**
```python
result = Account.delete_command("1234567", "/hello")
Bot.sendMessage(f"Command deletion result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Command deleted successfully"
}
```

#### edit_command

Updates the code of an existing command.

```python
Account.edit_command(botid, command, code)
```

**Parameters:**
- `botid` (Required): ID of the bot to edit the command for.
- `command` (Required): Name of the command to edit.
- `code` (Required): New code for the command.

**Example:**
```python
result = Account.edit_command("1234567", "/hello", "Bot.sendMessage('Updated hello message!')")
Bot.sendMessage(f"Command update result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Command updated successfully"
}
```

#### get_command_list

Retrieves a list of all commands for a bot.

```python
Account.get_command_list(botid)
```

**Parameters:**
- `botid` (Required): ID of the bot to get commands for.

**Example:**
```python
commands = Account.get_command_list("1234567")
if commands["ok"] and commands["result"]:
    for cmd in commands["result"]:
        Bot.sendMessage(f"Command: {cmd['command']}, Has code: {cmd['has_code']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": [
    {
      "command": "/start",
      "code_length": 256,
      "has_code": true
    },
    {
      "command": "/help",
      "code_length": 128,
      "has_code": true
    },
    {
      "command": "/settings",
      "code_length": 512,
      "has_code": true
    }
  ]
}
```

#### get_command_info

Retrieves detailed information about a specific command.

```python
Account.get_command_info(botid, command)
```

**Parameters:**
- `botid` (Required): ID of the bot the command belongs to.
- `command` (Required): Name of the command to get information for.

**Example:**
```python
info = Account.get_command_info("1234567", "/hello")
if info["ok"]:
    cmd_info = info["result"]
    Bot.sendMessage(f"Command code: {cmd_info['code']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": {
    "command": "/hello",
    "code": "Bot.sendMessage('Hello, world!')",
    "code_length": 31,
    "stats": {
      "executions": 342,
      "last_executed": "2023-05-15T14:30:22Z",
      "average_execution_time": 0.12
    }
  }
}
```

#### get_command_usage

Retrieves usage statistics for a specific command.

```python
Account.get_command_usage(botid, command, period="all")
```

**Parameters:**
- `botid` (Required): ID of the bot the command belongs to.
- `command` (Required): Name of the command to get usage for.
- `period` (Optional): Time period for statistics ("hour", "day", "week", "month", "all").

**Example:**
```python
usage = Account.get_command_usage("1234567", "/hello", "week")
if usage["ok"]:
    Bot.sendMessage(f"Command usage: {usage['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": {
    "command": "/hello",
    "period": "week",
    "executions": 123,
    "unique_users": 45,
    "average_execution_time": 0.18,
    "daily_stats": [
      {"date": "2023-05-10", "executions": 24},
      {"date": "2023-05-11", "executions": 31},
      {"date": "2023-05-12", "executions": 19},
      {"date": "2023-05-13", "executions": 27},
      {"date": "2023-05-14", "executions": 22}
    ]
  }
}
```

### User Management Methods

#### blockUser

Blocks a user from using a specific bot.

```python
Account.blockUser(user_id)
```

**Parameters:**
- `user_id` (Required): ID of the user to block.

**Example:**
```python
result = Account.blockUser("123456789")
Bot.sendMessage(f"Block result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "User blocked successfully"
}
```

#### unblockUser

Unblocks a previously blocked user.

```python
Account.unblockUser(user_id)
```

**Parameters:**
- `user_id` (Required): ID of the user to unblock.

**Example:**
```python
result = Account.unblockUser("123456789")
Bot.sendMessage(f"Unblock result: {result['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "User unblocked successfully"
}
```

#### getBlockedUsers

Retrieves a list of blocked users for a specific bot.

```python
Account.getBlockedUsers(botid)
```

**Parameters:**
- `botid` (Required): ID of the bot to get blocked users for.

**Example:**
```python
users = Account.getBlockedUsers("1234567")
if users["ok"] and users["result"]:
    for user in users["result"]:
        Bot.sendMessage(f"Blocked user: {user['user_id']}, Date: {user['blocked_date']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": [
    {
      "user_id": "123456789",
      "blocked_date": "2023-05-10T14:30:22Z"
    },
    {
      "user_id": "987654321",
      "blocked_date": "2023-05-12T09:15:43Z"
    }
  ]
}
```

#### getBlockedUsersFile

Generates a file containing blocked users information.

```python
Account.getBlockedUsersFile(botid=None, output_format="csv")
```

**Parameters:**
- `botid` (Optional): ID of the bot to get blocked users for. If omitted, gets all blocked users.
- `output_format` (Optional): Format of the output file ("csv" or "json").

**Example:**
```python
file = Account.getBlockedUsersFile("1234567", "json")
Bot.sendDocument(file)
```

**Example Output:**
```
# Returns a file-like object that can be directly passed to Bot.sendDocument()
# For CSV format, the file contains columns: user_id, blocked_date
# For JSON format, the file contains an array of objects with user_id and blocked_date fields
```

### Statistics and Reporting Methods

#### get_bot_stats

Retrieves comprehensive statistics for a specific bot.

```python
Account.get_bot_stats(botid)
```

**Parameters:**
- `botid` (Required): ID of the bot to get statistics for.

**Example:**
```python
stats = Account.get_bot_stats("1234567")
if stats["ok"]:
    bot_stats = stats["result"]
    Bot.sendMessage(f"Bot {bot_stats['name']}: {bot_stats['users']['total']} users, {bot_stats['users']['active_30d']} active")
```

**Example Output:**
```json
{
  "ok": true,
  "result": {
    "botid": "1234567",
    "name": "Support Bot",
    "username": "support_bot",
    "status": "working",
    "users": {
      "total": 1250,
      "active_30d": 450,
      "new_7d": 75,
      "blocked": 12
    },
    "commands": {
      "total": 15,
      "usage": {
        "start": 523,
        "help": 186,
        "settings": 94
      }
    },
    "points_used": 8765,
    "all_time_users": 1584
  }
}
```

#### get_bot_usage

Retrieves detailed usage statistics for a specific bot.

```python
Account.get_bot_usage(botid, period="all")
```

**Parameters:**
- `botid` (Required): ID of the bot to get usage for.
- `period` (Optional): Time period for statistics ("day", "week", "month", "all").

**Example:**
```python
usage = Account.get_bot_usage("1234567", "month")
if usage["ok"]:
    Bot.sendMessage(f"Bot usage: {usage['result']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": {
    "period": "month",
    "total_executions": 4325,
    "unique_users": 456,
    "commands": {
      "/start": 523,
      "/help": 186,
      "/settings": 94
    },
    "daily_stats": [
      {"date": "2023-05-01", "executions": 142, "users": 56},
      {"date": "2023-05-02", "executions": 156, "users": 62}
    ],
    "bot": {
      "botid": "1234567",
      "name": "Support Bot",
      "username": "support_bot",
      "status": "working"
    }
  }
}
```

#### get_bots_stats

Retrieves statistics for all bots in the account.

```python
Account.get_bots_stats()
```

**Example:**
```python
stats = Account.get_bots_stats()
if stats["ok"]:
    all_stats = stats["result"]
    Bot.sendMessage(f"Total bots: {all_stats['total_bots']}, Total users: {all_stats['total_users']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": {
    "total_bots": 5,
    "total_users": 2584,
    "total_commands": 68,
    "total_active_users": 943,
    "bot_stats": [
      {
        "botid": "1234567",
        "name": "Support Bot",
        "users": 1250,
        "active_users": 450
      },
      {
        "botid": "7654321",
        "name": "Quiz Bot",
        "users": 856,
        "active_users": 312
      }
    ]
  }
}
```

#### get_stats

Retrieves comprehensive account-level statistics.

```python
Account.get_stats()
```

**Example:**
```python
stats = Account.get_stats()
if stats["ok"]:
    account_stats = stats["result"]
    Bot.sendMessage(f"Account stats: {account_stats['bots']['total']} bots, {account_stats['users']['total']} users")
```

**Example Output:**
```json
{
  "ok": true,
  "result": {
    "email": "user@example.com",
    "plan": "Premium",
    "points_left": 8500,
    "points_used": 23450,
    "points_reset_at": "2023-06-01",
    "account_created": "2022-11-15 09:30:45",
    "bots": {
      "total": 5,
      "botids": ["1234567", "7654321", "2345678", "8765432", "3456789"]
    },
    "users": {
      "total": 2584,
      "active_30d": 943
    },
    "commands": {
      "total": 68
    }
  }
}
```

### Import/Export Methods

#### export_bot

Exports a bot's configuration and commands as a JSON file.

```python
Account.export_bot(botid)
```

**Parameters:**
- `botid` (Required): ID of the bot to export.

**Example:**
```python
try:
    file = Account.export_bot("1234567")
    Bot.sendDocument(file)
except ValueError as e:
    Bot.sendMessage(f"Export error: {str(e)}")
```

**Example Output:**
```
# Returns a file-like object that can be directly passed to Bot.sendDocument()
# The file contains a JSON object with the following structure:
# {
#   "bot": {
#     "botid": "1234567",
#     "bot_name": "Support Bot",
#     "bot_username": "support_bot",
#     "creation_date": "2023-05-01 14:30:22",
#     "_export_date": "2023-05-20 10:15:43"
#   },
#   "commands": [
#     {"command": "/start", "code": "Bot.sendMessage('Welcome!')"},
#     {"command": "/help", "code": "Bot.sendMessage('Help info')"}
#   ],
#   "global_data": [
#     {"name": "settings", "data": {"language": "en"}}
#   ],
#   "export_info": {
#     "date": "2023-05-20 10:15:43",
#     "exporter": "user@example.com",
#     "version": "1.0"
#   }
# }
```

#### import_bot

Imports a bot from an export file.

```python
Account.import_bot(import_data, new_token=None)
```

**Parameters:**
- `import_data` (Required): The JSON data from an exported bot.
- `new_token` (Optional): Token for the new bot.

**Example:**
```python
# Assuming import_data contains valid bot export data
result = Account.import_bot(import_data, "123456789:ABCDEF-ghijklmnopqrstuvwxyz")
if result["ok"]:
    Bot.sendMessage(f"Imported bot with ID: {result['botid']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "Bot imported successfully",
  "botid": "8765432",
  "command_count": 12
}
```

### API Management

#### revoke_api

Revokes the current API key and generates a new one.

```python
Account.revoke_api()
```

**Example:**
```python
result = Account.revoke_api()
if result["ok"]:
    Bot.sendMessage(f"New API key: {result['api_key']}")
```

**Example Output:**
```json
{
  "ok": true,
  "result": "API key revoked successfully and new key generated",
  "api_key": "abcdef1234567890abcdef1234567890"
}
```

## Enhanced Resource Management

### New accountRes Class

The 4.8.0 update introduces a new `accountRes` class for managing account-level resources, complementing the existing resource management system.

```python
res = libs.Resources.accountRes(name)
```

**Parameters:**
- `name` (Required): The name of the resource to manage.

### Resource Management Methods

All methods from the BaseRes class are available:

- `value()`: Gets the current value of the resource.
- `add(value)`: Adds to the resource value.
- `cut(value)`: Subtracts from the resource value.
- `set(value)`: Sets the resource to a specific value.
- `reset()`: Resets the resource value to zero.

**Example:**
```python
# Create an account-level resource
account_points = libs.Resources.accountRes("subscription_points")

# Add points
new_value = account_points.add(100)
Bot.sendMessage(f"Added points. New value: {new_value}")

# Check current value
current = account_points.value()
Bot.sendMessage(f"Current points: {current}")

# Use points
account_points.cut(50)
Bot.sendMessage(f"Used 50 points. Remaining: {account_points.value()}")
```

**Example Output:**
```
# For add():
100.0  # Returns the new value after addition

# For value():
100.0  # Returns the current value

# For cut():
50.0  # Returns the new value after subtraction

# For set():
200.0  # Returns the value that was set

# For reset():
0.0  # Always returns zero
```

## Server Improvements

The 4.8.0 update includes significant server-side improvements:

1. **Enhanced Stability**: Improved error handling and recovery mechanisms to prevent service disruptions.
2. **Optimized Performance**: Reduced response times and better resource allocation for smoother operation under high load.
3. **Improved Webhook Handling**: Faster and more reliable webhook processing for better bot responsiveness.
4. **Advanced Monitoring**: Better monitoring systems to detect and address issues before they affect users.

These improvements ensure that your bots remain operational and responsive, even during peak usage times or when handling complex commands.

## Command Aliases

The upcoming UI update will introduce support for command aliases, allowing multiple command triggers to execute the same code. This powerful feature will enable:

1. **Multi-language Support**: Create different command names for different languages.
2. **Command Variations**: Support both full and abbreviated versions of commands.
3. **Intuitive Interactions**: Allow users to trigger commands with natural language variations.

The alias system will be fully integrated into the command management system and accessible through both the UI and API.

## Bot Recovery System

The new bot recovery system allows users to recover accidentally deleted bots within 90 days, with features including:

1. **Temporary Deletion**: Bots are moved to a recovery collection rather than being permanently deleted.
2. **90-Day Recovery Window**: Generous timeframe to recover deleted bots.
3. **Statistics Tracking**: Monitor how many bots you've deleted and how many can be recovered.
4. **Expiration Management**: Clear visibility into when deleted bots will expire.

**Example:**
```python
# Get list of deleted bots
deleted_bots = Account.get_deleted_bots()
for bot_info in deleted_bots["result"]:
    Bot.sendMessage(f"Bot: {bot_info['name']}, Days remaining: {bot_info['days_remaining']}")

# Recover a bot
Account.recover_bot("1234567")

# Get stats about deleted bots
stats = Account.get_deleted_bots_stats()
Bot.sendMessage(f"Total deleted: {stats['result']['total']}, Recoverable: {stats['result']['recoverable']}")
``` 