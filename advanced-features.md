# Advanced Features

*Telebot Creator Documentation — Platform v7.1.2 · Telegram Bot API 10.1*

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
Bot.runCommandAfter(timeout, command, options=None)
```

**Parameters**:

* **`timeout`**: The delay before the command runs. Either a number of **seconds** or a `datetime` object. Allowed range: **1 second** minimum to **366 days** maximum.
* **`command`** (_str_): The command to execute when the timer fires.
* **`options`** (_Optional_): A value passed through to the scheduled command, available there as the `options` global.

The call returns a dict like `{"id": "<job_id>", "command": "<command>", "timeout": <seconds>}`. Save the `id` if you may want to cancel the task.

**Limits**:

* **60 schedules per minute** per user (rate limited).
* **50,000 outstanding scheduled tasks** per user.
* The bot must be in the `working` state when you schedule.

**Examples**:

1.  **Basic Scheduling**:

    ```python
    Bot.runCommandAfter(300, "reminder")  # Run the reminder command after 5 minutes
    ```
2.  **Passing data through**:

    ```python
    Bot.runCommandAfter(60, "send_reminder", options="meeting")  # available as `options`
    ```
3.  **Long-term Scheduling**:

    ```python
    Bot.runCommandAfter(2592000, "monthly_report")  # 30 days
    ```
4.  **Scheduling at a specific time** (pass a `datetime`):

    ```python
    from datetime import datetime, timedelta
    Bot.runCommandAfter(datetime.now() + timedelta(hours=2), "two_hour_followup")
    ```

#### **Bot.cancelScheduledTask**

Cancel a task you previously scheduled, using the `id` returned by `runCommandAfter`.

```python
job = Bot.runCommandAfter(3600, "send_reminder")
# later, if it is no longer needed:
Bot.cancelScheduledTask(job["id"])
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

**⚠️ Chain limit on `Bot.runCommand`**

One incoming user message may trigger a chain of at most **3 commands** through
`Bot.runCommand` without new user input. So `/a` → `/b` → `/c` runs, but a further
hop to `/d` is **refused silently** — the bot simply stops mid-flow with no error
shown to the user.

This exists to stop runaway loops. If you need a longer flow, do not chain:

* **`Bot.handleNextCommand`** — waits for the user to reply, and starts a fresh chain.
* **`Bot.runCommandAfter`** — a timer, and also starts a fresh chain.

Both are exempt from the 3-command limit by design, so interactive and scheduled
flows can be any length.

```python
# Chained (capped at 3)
Bot.runCommand("step_2")

# Not capped — a timer begins a new chain
Bot.runCommandAfter(1, "step_2")
```

The cap is tunable per deployment via the redis key `runcommand_flow_max`
(default `3`, `0` disables the guard).

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

#### **7.15 The `Account` Class**

The `Account` object (available in every command as `Account`) operates on **your whole account** rather than a single chat. Use it to manage bots, commands, blocked users, account-wide data, and stats. Methods generally return a dict shaped like `{"ok": True/False, "result": ...}`.

**Bot lifecycle**

```python
# Create a new bot from a token (verifies the token and starts it)
Account.create_bot(bot_token, bot_name=None, bot_username=None)

# Clone an existing bot's commands into a new bot (optionally with a token)
Account.clone_bot(botid, new_token=None)

# Start / stop / restart a bot you own
Account.start_bot(botid)
Account.stop_bot(botid)
Account.restart_bot(botid)

# Soft-delete (moves to the recycle bin), then later recover or purge it
Account.delete_bot(botid)                  # recoverable for 90 days
Account.get_deleted_bots()                 # list recoverable bots + days_remaining
Account.recover_bot(botid, new_token=None) # restore from recycle bin
Account.permanent_delete_bot(botid)        # purge immediately (irreversible)
```

> `delete_bot` is rate-limited to guard against malicious templates that loop over your bots; a normal user calling it a few times is fine.

**Export / import**

```python
# Export a bot to a file object (json | yaml | txt)
Account.export_bot(botid, format="json", include_bot_data=False)

# Re-create a whole bot from exported data
Account.import_bot(import_data, new_token=None, format="json")

# Import only commands into an existing bot
Account.import_commands(import_data, botid, remove_old_command=False, format="json")
```

See **7.16** for the export/import round-trip workflow.

**Command management**

```python
Account.create_command(botid, command, code)
Account.edit_command(botid, command, code)
Account.delete_command(botid, command)
Account.get_command_list(botid)            # names + code length + has_code
```

**User management**

```python
Account.blockUser(user_id)     # block (max 500 blocked users per account)
Account.unblockUser(user_id)
Account.getBlockedUsers(botid)
```

**Account-wide data** (shared across all your bots)

```python
Account.saveData("config", {"theme": "dark"})   # up to 10MB per key
value = Account.getData("config")
Account.deleteData("config")
```

**Statistics**

```python
# Active-user counts per time frame (e.g. "24h", "7d"); each frame max 365 days
Account.getStats(time_frames=["24h", "7d"], bot_ids=None)
# -> {"24h": 123, "7d": 456}
```

***

#### **7.16 Bot Export / Import and the AI Round-Trip**

Telebot Creator can export a bot's commands and (optionally) its global data to a portable file in **JSON**, **YAML**, or **TXT** format, and import it back. This is the basis of the "edit with AI" workflow.

**Export formats**

* `json` / `yaml` — structured: a `bot` block, a `commands` list (`command` + `code`), optional `global_data`, and an `export_info` block.
* `txt` — human-friendly. Each command is written as:

  ```text
  Command: <name>
  ---
  <code>
  ---
  ```

  The `---` lines fence each command's code; the importer reads commands back out of exactly this structure.

**The AI round-trip**

1. Open the bot's **Manage** tab and **Export** its commands (the backend route is `POST /v2/bots/{botid}/export-bot`).
2. Hand the exported file to an AI assistant and ask it to add, fix, or refactor commands — keeping the `Command: … / --- / code / ---` structure (for TXT) or the `commands` list shape (for JSON/YAML).
3. Bring the edited file back through **Import Commands** in the Manage tab (backend route `POST /v2/bots/{botid}/import-commands`). Tick "remove old commands" if you want a clean replace; otherwise existing commands with the same name are updated and new ones are added.

`import_bot` builds a brand-new bot from an export, while `import_commands` only merges commands into a bot that already exists.

***

#### **7.17 Folders**

Telebot Creator supports two kinds of folders to keep large accounts organized:

* **Bot folders** — group bots on your dashboard. Managed via the dashboard (backend routes under `/v2/bot-folders` and `/v2/bots/{botid}/move-to-folder`).
* **Command folders** — group commands inside a single bot's Commands view (backend routes under `/v2/bots/{botid}/commands/folders`).

Folders are an organizational layer only; they do not change how commands run.

***

#### **7.18 Recycle Bin and Bot Recovery**

Deleting a bot is a **soft delete**: the bot is stopped and moved to a recycle bin instead of being erased. You have a **90-day window** to restore it (commands and data come back with it).

* From the dashboard: **Recycle Bin** lists deleted bots with the days remaining; restore or permanently delete from there.
* From TPY: `Account.get_deleted_bots()`, `Account.recover_bot(botid)`, and `Account.permanent_delete_bot(botid)`.

After 90 days a deleted bot becomes eligible for permanent cleanup and can no longer be recovered.

***

#### **7.19 The `.env` Command (Injected Globals)**

Create a command named **`.env`** to define configuration that is injected as global variables into every command's execution. Each line is `KEY = value`; values are parsed as Python literals when possible (strings, numbers, lists, dicts, tuples), otherwise kept as text. Multi-line list/dict values are supported, and `#` lines are comments.

```text
API_KEY = "sk-abc123"
MAX_RETRIES = 3
ADMINS = [12345, 67890]
WELCOME = "Hello and welcome!"
```

Then use them directly in any command:

```python
bot.sendMessage(WELCOME)
if int(u) in ADMINS:
    bot.sendMessage("You're an admin.")
```

Keys cannot start with `__` (e.g. `__builtins__` is rejected), and values cannot contain forbidden/unsafe expressions.

***

#### **7.20 Built-in Globals Reference**

Beyond `bot`, `Bot`, `User`, `Account`, `message`, `msg`, `params`, `u`, `options`, `command`, `left_points`, and `libs`, every command runs with these helpers already available — no import needed:

| Global | What it is |
| --- | --- |
| `HTTP` | A ready-to-use HTTP client for outbound requests. |
| `CSV` | CSV helper for building/reading CSV data. |
| `bunchify` | Wraps a dict so you can use attribute access (`d.key`). |
| `regex` / `re` | Regular-expression module. |
| `web3_` | A configured Web3 instance for EVM chains. |
| `TBC_Web3_` | The `web3lib` library (same as `libs.web3lib`). |
| `ReturnCommand` (also `returnCommand` / `returncommand`) | Stop the current command and hand control to another command. |
| `MembershipCheck` | Check whether a user is a member of given channels/groups: `MembershipCheck(channels, u)`. |
| `isNumeric` | Return whether a value is numeric. |
| `jsondumps` | Serialize a value to a JSON string. |
| `encodeURIComponent` / `decodeURIComponent` / `rawurlencode` | URL encoding/decoding helpers. |
| `parse_qs` | Parse a query string into a dict. |
| `md5`, `hashlib`, `base64`, `binascii`, `time` | Common hashing/encoding/time utilities. |

For security, names like `eval`, `exec`, `open`, `os`, `subprocess`, `sys`, `globals`, and `locals` are disabled inside command code.

***

#### **7.21 Resources**

`libs.Resources` provides counters/balances you can attach to users or to your whole account:

* **`userRes(name, user)`** — a per-user resource (points, credits, quota, …).
* **`accountRes`**, **`globalRes`**, **`anotherRes`**, **`adminRes`** — account-wide / cross-scope resources.

Every resource object supports `add`, `cut`, `set`, `reset`, `value`, `getAllData`, and `fetchAllResources`:

```python
points = libs.Resources.userRes("points", u)
points.add(10)
points.cut(3)
bot.sendMessage(f"Balance: {points.value()}")

leaderboard = libs.Resources.userRes("points").getAllData(5)  # top 5
```

The **Libraries** doc covers `libs.Resources` in depth.

***

####
