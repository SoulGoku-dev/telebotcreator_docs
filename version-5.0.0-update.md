# Version 5.0.0 Update

We're excited to announce the release of TeleBot Creator 5.0.0, featuring significant improvements to performance, stability, and functionality.

## Major Improvements

### ⏱️ Extended Command Runtime
- Commands can now run up to 160 seconds (increased from 120 seconds)
- Performance optimizations for better command execution
- Major bug fixes improving overall stability

### 📊 New Statistics Functions
Account and Bot classes now include powerful statistics tracking capabilities:

#### Account.getStats
This function allows you to retrieve user statistics across multiple bots in your account:

```python
# Get user statistics for all bots in your account
stats = Account.getStats()

# Get user statistics for specific time frames
stats = Account.getStats(time_frames=["24h", "7d", "30d"])

# Get user statistics for specific bots
stats = Account.getStats(bot_ids=["bot123456", "bot654321"])

# Combine both parameters
stats = Account.getStats(time_frames=["24h", "7d"], bot_ids=["bot123456"])
```

**Parameters:**
- `time_frames`: List of time frames to query (optional, default: ["24h"])
  - Format: "<number>h" for hours or "<number>d" for days (e.g., "24h", "7d")
  - Maximum time frame: 365 days
- `bot_ids`: List of bot IDs to query (optional, default: all bots in account)

**Returns:**
- Dictionary with time frames as keys and active user counts as values
- Example: `{"24h": 150, "7d": 350}`

#### Bot.getStats
Similar to Account.getStats but for a specific bot:

```python
# Get user statistics for current bot
stats = Bot.getStats()

# Get user statistics for specific time frames
stats = Bot.getStats(time_frames=["24h", "7d", "30d"])

# Get statistics for a specific bot with API key authentication
stats = Bot.getStats(bot_id="bot123456", api_key="your_api_key")
```

**Parameters:**
- `time_frames`: List of time frames to query (optional, default: ["24h"])
- `bot_id`: Bot ID to query (optional, default: current bot)
- `api_key`: API key for authentication when querying other bots (optional)

**Returns:**
- Dictionary with time frames as keys and active user counts as values
- Example: `{"24h": 150, "7d": 350}`

### 🔄 Data Transfer Functionality
The new TransferData function in the Account class allows you to transfer bot data between bots:

```python
# Transfer data from one bot to another
result = Account.TransferData(from_bot="source_bot_id", to_bot="destination_bot_id")
```

**Parameters:**
- `from_bot`: Source bot ID to transfer data from
- `to_bot`: Destination bot ID to transfer data to

**Returns:**
- Dictionary with operation status
- Success: `{"ok": True, "result": "Data transferred successfully"}`
- Failure: `{"ok": False, "result": "Error message"}`

### 🤖 OpenRouter AI Integration
OpenRouter API is now fully supported through the openai_lib, with enhanced timeout capabilities:

```python
# Initialize OpenAI client with OpenRouter
API_KEY = "YOUR_OPENROUTER_API_KEY"
MESSAGE = "hello"

# Create client with extended timeout (up to 160 seconds)
client = libs.openai_lib.OpenAIClient(api_key=API_KEY, timeout=120)

# Initialize AI assistant with specific model
assistant = libs.openai_lib.AIAssistant(
    openai_client=client,
    model="meta-llama/llama-3.3-8b-instruct:free",
    system_message="You're helpful assistant."
)

# Send message and get response
response = assistant.send_message(MESSAGE)
response_text = str(response.get("content")[0]['text']['value'])
bot.sendMessage(response_text)
```

**Key Features:**
- Support for OpenRouter API with access to multiple AI models
- Extended timeout up to 160 seconds (configurable)
- Automatic error handling and retries
- System message customization
- Compatible with various models including Meta's Llama models

## Stability Improvements
- Enhanced bot optimization for better performance
- Improved error handling and logging
- Memory usage optimizations
- Fixed issues with long-running commands

## Upgrading
To take advantage of these new features, simply restart your bot or create a new one. All improvements are automatically available in your workspace.

## Feedback
As always, we value your feedback. If you encounter any issues or have suggestions for further improvements, please let us know through our support channels.
