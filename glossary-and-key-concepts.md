# Glossary and Key Concepts

*Telebot Creator Documentation — Platform v7.1.2 · Telegram Bot API 10.1*

*Last updated: June 2026 | Maintained by Telebot Creator Team*

Quick reference for key terms and concepts used in Telebot Creator.

---

### **11.2 Key Terms**

#### **1. Bot API Token**

* **Definition**: A unique token provided by Telegram for authenticating and managing a bot. This token is required to link your bot with Telebot Creator.
* **Where to Get It**: Use @BotFather on Telegram to create a new bot and retrieve its token.

***

#### **2. TPY (Telebot Python)**

* **Definition**: A customized version of Python designed specifically for Telebot Creator. TPY simplifies bot development by offering built-in libraries, pre-defined variables, and a restricted, secure environment.
*   **Example**:

    ```python
    bot.sendMessage("Welcome to my bot!")
    ```

***

#### **3. Commands**

* **Definition**: Triggers in a bot that execute specific logic when a user sends a corresponding message. Commands typically start with a `/` (e.g., `/start`, `/help`).
*   **Example**:

    ```python
    def start_command():
        bot.sendMessage("Hello! This is the start command.")
    ```

***

#### **4. Points**

* **Definition**: The internal currency of Telebot Creator used to execute bot operations. Each command execution costs 1 point.
* **Monthly Allocation**: Users receive 100,000 points per month for free.
*   **Usage**:

    ```python
    points = left_points
    bot.sendMessage(f"You have {points} points remaining.")
    ```

***

#### **5. Broadcast**

* **Definition**: A feature that sends messages or executes commands across multiple users simultaneously.
*   **Example**:

    ```python
    Bot.broadcast(
        function="send_message",
        text="Hello, everyone!"
    )
    ```

***

#### **6. Webhook**

* **Definition**: A URL that allows bots to receive real-time updates from external systems or trigger commands dynamically.
*   **Example**:

    ```python
    webhook_url = libs.Webhook.getUrlFor(
        command="process_data",
        user_id=12345
    )
    bot.sendMessage(f"Webhook URL: {webhook_url}")
    ```

***

#### **7. Transfer**

* **Definition**: The process of transferring ownership of a bot from one Telebot Creator account to another.
*   **Example**:

    ```python
    result = Bot.Transfer(
        email="newowner@example.com",
        bot_id="123456",
        bot_token="API_TOKEN",
        run_now=True
    )
    bot.sendMessage(f"Bot successfully transferred to {result['bot_id']}.")
    ```

***

### **11.3 Libraries and Integrations**

#### **1. libs.CSV**

* **Definition**: A library for managing CSV files. Useful for storing and retrieving structured data like leaderboards or survey responses.
*   **Example**:

    ```python
    csv_handler = libs.CSV.CSVHandler("data.csv")
    csv_handler.create_csv(["Name", "Points"])
    csv_handler.add_row({"Name": "Alice", "Points": 100})
    ```

***

#### **2. libs.Coinbase**

* **Definition**: A library for handling cryptocurrency payments using Coinbase.
*   **Example**:

    ```python
    libs.Coinbase.setKeys("API_KEY", "SECRET")
    client = libs.Coinbase.post()
    payment = client.createCharge({
        "name": "Subscription",
        "description": "Monthly fee",
        "local_price": {"amount": "10.00", "currency": "USD"},
        "pricing_type": "fixed_price"
    })
    bot.sendMessage(f"Pay here: {payment['hosted_url']}")
    ```

***

#### **3. libs.Webhook**

* **Definition**: A library for generating and managing webhook URLs.
*   **Example**:

    ```python
    webhook_url = libs.Webhook.getUrlFor(
        command="update_status",
        user_id=67890
    )
    bot.sendMessage(f"Webhook URL: {webhook_url}")
    ```

***

#### **4. libs.web3lib (EVM Blockchain)**

* **Definition**: A library for sending ETH/tokens on any EVM-compatible blockchain (Ethereum, Polygon, Arbitrum, BSC, etc.). Replaces deprecated `libs.Polygon`, `libs.ARB`, `libs.TTcoin`, and `libs.Tomochain`.
*   **Example**:

    ```python
    libs.web3lib.sendETHER(
        private_key="PRIVATE_KEY",
        to="0xRecipientAddress",
        value=0.01,
        chain="polygon"
    )
    ```

***

### **11.4 Advanced Concepts**

#### **1. Multi-Step Workflows**

* **Definition**: A sequence of commands executed step-by-step based on user input.
*   **Example**:

    ```python
    bot.sendMessage("What’s your name?")
    Bot.handleNextCommand("save_name")
    ```

***

#### **2. Callback URLs**

* **Definition**: URLs used in broadcasts and webhooks to receive execution feedback or trigger additional processes.
*   **Example**:

    ```python
    Bot.broadcast(
        function="send_message",
        text="Thank you for subscribing!",
        callback_url="https://example.com/callback"
    )
    ```

***

#### **3. Sandbox Environment**

* **Definition**: A secure environment where bot commands are executed to prevent unauthorized actions or access.

***

#### **4. Global Broadcast Limits**

* **Definition**: A system-wide limit of 5000 simultaneous broadcasts across all bots to ensure server stability, plus a per-user limit of 3 concurrent broadcasts.

***

### **11.5 Usage Examples**

#### **Broadcast Syntax**

```python
Bot.broadcast(
    function="send_message",
    text="Hello, everyone!"
)
```

#### **Webhook Generation**

```python
webhook_url = libs.Webhook.getUrlFor(
    command="process_data",
    user_id=12345
)
bot.sendMessage(f"Webhook URL: {webhook_url}")
```

#### **Dynamic Data Fetching**

```python
response = HTTP.get("https://api.example.com/data")
bot.sendMessage(f"API Response: {response.json()}")
```

***

#### **5. Account Class**

* **Definition**: A globally available class (since v4.8.0) for managing account-level operations across all bots — list bots, save/get shared data, get stats, and transfer data between bots.
*   **Example**:

    ```python
    stats = Account.getStats(time_frames=["24h", "7d"])
    bot.sendMessage(f"Active users: {stats}")
    ```

***

#### **6. libs.openai_lib / libs.gemini_lib**

* **Definition**: Built-in AI libraries for integrating OpenAI (GPT-4o, Assistants API) and Google Gemini models into your bots.
*   **Example**:

    ```python
    client = libs.openai_lib.OpenAIClient(api_key="KEY")
    assistant = libs.openai_lib.AIAssistant(
        openai_client=client,
        model="gpt-4o",
        system_message="You are helpful."
    )
    response = assistant.send_message(msg)
    ```
