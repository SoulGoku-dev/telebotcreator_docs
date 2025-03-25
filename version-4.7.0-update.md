# Version 4.7.0 Update

## New Features Overview

The version 4.7.0 update introduces several powerful features to enhance your bot development experience:

1. **New User and Bot Class Methods**: Additional methods for data file handling and user information.
2. **New AI Integration Libraries**: OpenAI and Gemini AI libraries for advanced AI capabilities.
3. **Enhanced Resource Management**: New functions in the Resources library.

## New User.x Class Methods

The following methods have been added to the `User.x` class for better data handling:

### getDataFile

Retrieves stored user data as a file, which can be sent to users directly.

```
User.getDataFile(name, user=None, output_format="txt")
```

**Parameters:**
- `name` (Required): The name of the data to retrieve.
- `user` (Optional): The user ID to get data for. Defaults to current user if not specified.
- `output_format` (Optional): Format of the output file (currently supports "txt").

**Example:**
```
file = User.getDataFile("profile")
bot.sendDocument(file)
```

### getAllData

Retrieves all data entries that match a specific name pattern and returns them as a JSON file.

```
User.getAllData(name, output_format="json")
```

**Parameters:**
- `name` (Required): The name pattern to search for.
- `output_format` (Optional): Format of the output file (currently supports "json").

**Example:**
```
data_file = User.getAllData("settings")
bot.sendDocument(data_file)
```

### getAllDataOfUser

Retrieves all data associated with a specific user.

```
User.getAllDataOfUser(user, output_format="json")
```

**Parameters:**
- `user` (Required): The user ID to get all data for.
- `output_format` (Optional): Format of the output file (currently supports "json").

**Example:**
```
user_data = User.getAllDataOfUser("12345678")
bot.sendDocument(user_data)
```

## New Bot.x Class Methods

The following methods have been added to the `Bot.x` class for better global data handling:

### getDataFile

Retrieves stored global data as a file.

```
Bot.getDataFile(name, output_format="txt")
```

**Parameters:**
- `name` (Required): The name of the global data to retrieve.
- `output_format` (Optional): Format of the output file (currently supports "txt").

**Example:**
```
config_file = Bot.getDataFile("config")
bot.sendDocument(config_file)
```

### getAllData

Retrieves all global data entries that match a specific name pattern. 

**Note:** This method only works with data saved after the 4.7.0 update. It cannot access older data.

```
Bot.getAllData(name, output_format="json")
```

**Parameters:**
- `name` (Required): The name of the global data to retrieve.
- `output_format` (Optional): Format of the output file (currently supports "json").

**Example:**
```
all_configs = Bot.getAllData("config")
bot.sendDocument(all_configs)
```

### getBotUsersFile

Retrieves information about all users of the bot in either CSV or JSON format.

```
Bot.getBotUsersFile(output_format="json", include_creation_date=False, include_last_active_date=False)
```

**Parameters:**
- `output_format` (Optional): Format of the output file ("json" or "csv").
- `include_creation_date` (Optional): Whether to include user creation date in the output.
- `include_last_active_date` (Optional): Whether to include the user's last active date.

**Example:**
```
users_file = Bot.getBotUsersFile(output_format="csv", include_creation_date=True)
bot.sendDocument(users_file)
```

## New AI Libraries

### libs.openai_lib

Integrates OpenAI's API for powerful AI capabilities. Supports chat completions and the OpenAI Assistants API.

#### Key Classes:

1. **OpenAIClient**: Core client for interacting with OpenAI API.
2. **AIAssistant**: High-level wrapper for working with OpenAI Assistants.

**Example - Chat Completion:**
```
client = libs.openai_lib.OpenAIClient(api_key="YOUR_API_KEY")
response = client.create_chat_completion(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Tell me about Telegram bots."}
    ]
)
bot.sendMessage(response["choices"][0]["message"]["content"])
```

**Example - Assistant:**
```
client = libs.openai_lib.OpenAIClient(api_key="YOUR_API_KEY")
assistant = libs.openai_lib.AIAssistant(
    openai_client=client,
    create_new=True,
    name="Customer Support Bot",
    instructions="You are a helpful customer support assistant.",
    model="gpt-4o"
)

thread_id = assistant.start_conversation()
response = assistant.send_message("How do I reset my password?")
bot.sendMessage(response["content"])
```

### libs.gemini_lib

Integrates Google's Gemini AI models using an OpenAI-compatible API interface.

#### Key Classes:

1. **GeminiClient**: Core client for interacting with Gemini API.
2. **GeminiAIAssistant**: High-level wrapper for working with Gemini in an assistant-like way.

**Example - Chat Completion:**
```
client = libs.gemini_lib.GeminiClient(api_key="YOUR_API_KEY")
response = client.create_chat_completion(
    model="gemini-2.0-flash",
    messages=[
        {"role": "user", "content": "What are the best practices for Telegram bot development?"}
    ]
)
bot.sendMessage(response["choices"][0]["message"]["content"])
```

**Example - Assistant:**
```
client = libs.gemini_lib.GeminiClient(api_key="YOUR_API_KEY")
assistant = libs.gemini_lib.GeminiAIAssistant(
    gemini_client=client,
    create_new=True,
    name="Product Advisor",
    instructions="You are a helpful product recommendation assistant.",
    model="gemini-2.0-flash"
)

thread_id = assistant.start_conversation()
response = assistant.send_message("I need a new laptop for video editing.")
bot.sendMessage(response["content"])
```

## Enhanced Resource Management

The 4.7.0 update adds significant improvements to the `libs.Resources` library with new administrative capabilities:

### New adminRes Class

A new `adminRes` class has been added to provide administrative control over resources:

```
admin = libs.Resources.adminRes(name, user=None)
```

**Parameters:**
- `name` (Required): The name of the resource to manage.
- `user` (Optional): User ID to scope the admin operations.

### New Administrative Methods

The following methods are available with the adminRes class:

#### clearAllData

Clears all resource data, optionally for a specific user.

```
admin.clearAllData(user=None)
```

**Example:**
```
admin = libs.Resources.adminRes("points")
admin.clearAllData("12345678")  # Clear for specific user
admin.clearAllData()  # Clear for all users
```

#### fetchAllResourcesOfUser

Retrieves all resources for a specific user as a file (JSON or CSV).

```
admin.fetchAllResourcesOfUser(user, output_format)
```

**Parameters:**
- `user` (Required): User ID to get resources for.
- `output_format` (Required): Format of the output file ("json" or "csv").

**Example:**
```
resources_file = admin.fetchAllResourcesOfUser("12345678", "json")
bot.sendDocument(resources_file)
```

#### removeDataOfUser

Removes specific resource data for a user.

```
admin.removeDataOfUser(user)
```

**Example:**
```
admin = libs.Resources.adminRes("points")
admin.removeDataOfUser("12345678")
```

#### removeAllDataOfUser

Removes all resource data for a user.

```
admin.removeAllDataOfUser(user)
```

**Example:**
```
admin = libs.Resources.adminRes("points")
admin.removeAllDataOfUser("12345678")
```

#### removeAllData

Removes all resource data for the bot.

```
admin.removeAllData()
```

**Example:**
```
admin = libs.Resources.adminRes("points")
admin.removeAllData()
```

## Summary

Version 4.7.0 dramatically expands the capabilities of Telebot Creator with:

1. **New Data Management Functions**: Added `getDataFile`, `getAllData`, and `getAllDataOfUser` to the User.x class and `getDataFile`, `getAllData`, and `getBotUsersFile` to the Bot.x class, making it easier to work with data and export it in different formats.

2. **Powerful AI Integration**: Introduced OpenAI and Gemini libraries for advanced AI capabilities, allowing bots to leverage state-of-the-art language models through simple interfaces.

3. **Enhanced Resource Management**: Added the new `adminRes` class to libs.Resources with administrative methods like `clearAllData`, `fetchAllResourcesOfUser`, `removeDataOfUser`, `removeAllDataOfUser`, and `removeAllData` for better control over resources.

These additions make it easier to build sophisticated bots with advanced data handling, resource management, and AI capabilities.

Remember that the new `Bot.getAllData()` method only works with data saved after this update, as it requires a specific data structure not present in older versions. 