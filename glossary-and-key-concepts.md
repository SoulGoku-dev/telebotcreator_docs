# Glossary and Key Concepts

#### **11. Glossary and Key Concepts**

This section provides definitions and explanations of key terms and concepts in Telebot Creator. Understanding these terms is crucial for effectively building and managing bots on the platform.

***

### **11.1 Introduction**

The Telebot Creator platform uses a variety of specialized terms and concepts to define its functionality. This glossary serves as a quick reference for users, ensuring they have a clear understanding of the platform's core elements and advanced features.

***

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

#### **4. libs.Polygon**

* **Definition**: A library for managing cryptocurrency transactions on the Polygon network.
*   **Example**:

    ```python
    libs.Polygon.setKeys("PRIVATE_KEY")
    libs.Polygon.send(
        value=5,
        to="0xRecipientAddress",
        contract="TokenContractAddress"
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

* **Definition**: A system-wide limit of 1000 simultaneous broadcasts across all bots to ensure server stability.

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
http_client = libs.customHTTP()
response = http_client.get("https://api.example.com/data")
bot.sendMessage(f"API Response: {response.json()}")
http_client.close()
```

***

####
