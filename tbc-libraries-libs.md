# TBC Libraries (Libs)

Libraries, often called **libs**, in Telebot Creator (TBC) enhance the basic TPY functionalities by offering pre-built modules for specific tasks. These libraries make it easier and faster to add advanced features to your Telegram bots, such as handling payments, managing blockchain transactions, working with data, generating random values, and more.\
\
**DEPRECATION NOTICE:**

The following libraries are deprecated and removed:

`libs.Polygon, libs.ARB, libs.TTcoin, libs.Tomochain.`

Please migrate to libs.web3lib (sendETHER) which now supports all EVM chains, proxy support, and enhanced functionality.

#### **5.1 Overview of Libraries**

TBC libraries are grouped based on their functionalities to help you develop bots efficiently:

* **Payment Integrations**: Automate payments using popular services.
* **Blockchain Transactions**: Manage cryptocurrency transfers and transactions.
* **Data Handling**: Work with data easily using CSV files.
* **Randomization**: Generate random numbers or strings for games and giveaways.
* **Resource Management**: Track points, credits, or other resources for users or globally.

#### **5.2 Key Libraries and Their Functions**

Here are the main libraries available in TBC, along with their key functions and examples of how to use them:

**1. libs.Resources**

Manages numeric values tied to users or globally for tracking points or credits.

* **Classes**:
  * `userRes(name, user)`: Manages resources for a specific user.
  * `globalRes(name)`: Manages global resources.
  * `adminRes(name, user=None)`: Provides administrative access to resources.
* **Basic Methods**:
  * `value()`: Gets the current value.
  * `add(amount)`: Adds to the resource.
  * `cut(amount)`: Subtracts from the resource.
  * `set(amount)`: Sets the resource to a specific value.
  * `reset()`: Resets the resource to zero.
  * `getAllData(length)`: Retrieves top resource entries.
* **Admin Methods** (available with adminRes):
  * `clearAllData(user=None)`: Clears all resource data, optionally for a specific user.
  * `fetchAllResourcesOfUser(user, output_format)`: Retrieves all resources for a specific user as a file.
  * `removeDataOfUser(user)`: Removes specific resource data for a user.
  * `removeAllDataOfUser(user)`: Removes all resource data for a user.
  * `removeAllData()`: Removes all resource data for the bot.
*   **Example - Basic Usage**:

    ```python
    points = libs.Resources.userRes("points", user)
    points.add(10)
    current_points = points.value()
    bot.sendMessage(f"You now have {current_points} points!")
    ```
    
*   **Example - Admin Operations**:

    ```python
    # Create admin resource manager
    admin = libs.Resources.adminRes("points")
    
    # Fetch all resources for a specific user
    user_resources = admin.fetchAllResourcesOfUser("12345678", "json")
    bot.sendDocument(user_resources)
    
    # Clear all data for a specific user
    admin.clearAllData("12345678")
    bot.sendMessage("All resource data cleared for user")
    ```

**2. libs.CSV**

Simplifies data management with CSV files.

* **Class**:
  * `CSVHandler(filename)`: Manages a specific CSV file.
* **Methods**:
  * `create_csv(headers)`: Creates a new CSV file with headers.
  * `add_row(row)`: Adds a new row of data.
  * `edit_row(row_index, row)`: Edits an existing row.
  * `get()`: Retrieves information about the CSV file.
  * `delete()`: Deletes the CSV file.
*   **Example**:

    ```python
    csv = libs.CSV.CSVHandler("data.csv")
    csv.create_csv(["Name", "Points", "Date"])
    csv.add_row({"Name": "Alice", "Points": 100, "Date": "2025-01-01"})
    ```

**3. libs.Coinbase**

Integrates with the Coinbase payment gateway for cryptocurrency transactions.

* **Functions**:
  * `setKeys(api_key, secret)`: Sets your Coinbase API keys.
  * `post(api_key=None, secret=None)`: Returns a Coinbase client instance.
*   **Example**:

    ```python
    libs.Coinbase.setKeys("your_api_key", "your_secret_key")
    client = libs.Coinbase.post()
    ```

**4. libs.Crypto**

Provides tools for cryptocurrency conversions and pricing.

* **Functions**:
  * `convert(_from, _to, _amount)`: Converts an amount from one cryptocurrency to another.
  * `get_price(currency, price_in)`: Gets the current price of a cryptocurrency.
*   **Example**:

    ```python
    price = libs.Crypto.get_price("BTC", "USD")
    bot.sendMessage(f"The current price of BTC in USD is {price}.")
    ```

**5. libs.Random**

Generates random numbers and strings for various uses.

* **Functions**:
  * `randomInt(min, max)`: Returns a random integer between min and max.
  * `randomStr(length, char_set=None)`: Returns a random string of the specified length.
  * `randomFloat(min, max)`: Returns a random float between min and max.
  * `randomAscii(length)`: Returns a random ASCII string.
*   **Example**:

    ```python
    random_number = libs.Random.randomInt(1, 100)
    bot.sendMessage(f"Your random number is: {random_number}")

    random_string = libs.Random.randomStr(8)
    bot.sendMessage(f"Your random string is: {random_string}")
    ```

**7.  libs.Paytm**

Integrates with the Paytm payment gateway for fund transfers and automated payments.

* **Functions**:
  * `setKeys(key, mid, token)`: Sets your Paytm merchant credentials.
  * `send(amount, number, description=None, key=None, mid=None, token=None)`: Sends a payment to a phone number.
*   **Example**:

    ```python
    libs.Paytm.setKeys("merchant_key", "merchant_id", "auth_token")
    libs.Paytm.send(100, "9876543210", "Payment for services")
    ```

**8. libs.Webhook**

The `libs.Webhook` library in Telebot Creator allows you to create and manage webhooks for seamless external integrations. Webhooks enable bots to respond to real-time updates from external systems, such as payment notifications or user actions.

***

**Functions**

1. **`getUrlFor(command, user_id=None, chat_id=None, bot_id=None, api_key=None, **options)`**
   * **Description**: Generates a webhook URL for invoking a specific command, optionally tied to a specific user, chat, or another bot.
   *   **Function Syntax**:

       ```python
       libs.Webhook.getUrlFor(
           command: str,
           user_id: Optional[str] = None,
           chat_id: Optional[str] = None,
           bot_id: Optional[str] = None,
           api_key: Optional[str] = None,
           **options: Any
       )
       ```
   * **Parameters**:
     * **`command`** (_str_): The command to be triggered by the webhook.
     * **`user_id`** (_Optional\[str]_): The user ID associated with the webhook.
     * **`chat_id`** (_Optional\[str]_): The chat ID associated with the webhook.
     * **`bot_id`** (_Optional\[str]_): The ID of the bot for which the webhook is being generated. If provided, **`api_key`** is required.
     * **`api_key`** (_Optional\[str]_): The API key of the bot owner. Used for validation when `bot_id` is specified.
     * **`**options`**: Additional options to customize the webhook behavior.
   * **In webhook commands**, the incoming response data is stored in `options`, not `message`. The `options` dictionary includes two keys:
     * **`data`**: The raw incoming data.
     * **`json`**: A parsed JSON object from the webhook payload.
   *   **Examples**:

       **Basic Webhook URL**:

       ```python
       webhook_url = libs.Webhook.getUrlFor("process_payment", user_id=12345)
       bot.sendMessage(f"Webhook URL: {webhook_url}")
       ```

       **Webhook for Another Bot**:

       ```python
       webhook_url = libs.Webhook.getUrlFor(
           "process_payment",
           user_id=12345,
           bot_id="another_bot_id",
           api_key="another_bot_api_key"
       )
       bot.sendMessage(f"Webhook URL for another bot: {webhook_url}")
       ```

       **Webhook with Additional Options**:

       ```python
       webhook_url = libs.Webhook.getUrlFor(
           "notify_event",
           chat_id=67890,
           options={"options": "example", "redirect_to": "https://example.com/callback"}
       )
       bot.sendMessage(f"Custom Webhook URL: {webhook_url}")
       ```

***

**Use Cases**

* **Payment Confirmation**: Generate webhook URLs for real-time payment updates from external systems.
* **Bot-to-Bot Communication**: Use `bot_id` and `api_key` to enable bots to trigger commands in each other.
* **Dynamic Event Handling**: Pass additional options to customize webhook behavior based on the event.

**9. libs.DateAndTime**

Works with dates and times.

* **Functions**:
  * `utcnow()`: Gets the current UTC date and time.
  * `date_now()`: Gets the current UTC date.
  * `time()`: Gets the current UNIX timestamp.
  * `now(timezone_str)`: Gets the current date and time in a specific timezone.
*   **Example**:

    ```python
    current_time = libs.DateAndTime.utcnow()
    bot.sendMessage(f"The current UTC time is: {current_time}")
    ```

**10. libs.Oxapay**

Integrates the Oxapay payment system for merchants.

* **Functions**:
  * `post(merchant_api_key)`: Initializes the Oxapay client with your API key.
*   **Example**:

    ```python
    client = libs.Oxapay.post("your_merchant_api_key")
    bot.sendMessage("Oxapay client initialized!")
    ```

**11. libs.customHTTP**

Performs HTTP requests with limitations on data size and timeouts, we use it as HTTP class but if you want you can use this lib, this lib provides 30 seconds timeout and it can receive contene upto 20 mb.

* **Class**:
  * `CustomHTTP()`: Creates an HTTP client.
* **Methods**:
  * `get(url, **kwargs)`: Performs a GET request.
  * `post(url, **kwargs)`: Performs a POST request.
  * `put(url, **kwargs)`: Performs a PUT request.
  * `head(url, **kwargs)`: Performs a HEAD request.
  * `options(url, **kwargs)`: Performs an OPTIONS request.
  * `patch(url, **kwargs)`: Performs a PATCH request.
  * `close()`: Closes the HTTP session.
*   **Example**:

    ```python
    http_client = libs.customHTTP()
    response = http_client.get("https://api.example.com/data")
    bot.sendMessage(f"Response: {response}")
    # not close the connect for future requests
    ```

**12. libs.web3lib**

**Overview:**\
This library is designed to interact with all supported EVM chains. It offers functions to send native coins and tokens (ERC‑20) using advanced features such as automatic gas estimation, retry logic, and optional proxy support. This library is published on Telebot Creator in TPY language.

**Supported Networks (31 Total):**

* **Ethereum** (chainId: 1)
* **BSC** (chainId: 56)
* **Polygon** (chainId: 137)
* **Avalanche** (chainId: 43114)
* **Fantom** (chainId: 250)
* **Arbitrum** (chainId: 42161)
* **Optimism** (chainId: 10)
* **Harmony** (chainId: 1666600000)
* **Cronos** (chainId: 25)
* **Moonriver** (chainId: 1285)
* **Moonbeam** (chainId: 1284)
* **Celo** (chainId: 42220)
* **Heco** (chainId: 128)
* **Okexchain** (chainId: 66)
* **Xdai** (chainId: 100)
* **KCC** (chainId: 321)
* **Metis** (chainId: 1088)
* **Aurora** (chainId: 1313161554)
* **Base** (chainId: 8453)
* **ZKSync** (chainId: 324)
* **Scroll** (chainId: 534352)
* **Linea** (chainId: 59144)
* **Boba** (chainId: 288)
* **Kava** (chainId: 2222)
* **Fuse** (chainId: 122)
* **Evmos** (chainId: 9001)
* **Canto** (chainId: 7700)
* **Astar** (chainId: 592)
* **Telos** (chainId: 40)
* **Rootstock** (chainId: 30)
* **TTcoin** (chainId: 22023)

**Key Functions:**

* **get\_default\_rpc(network: str) -> str**\
  Returns the default RPC URL for the given network.
* **get\_supported\_networks() -> Dict\[str, Dict\[str, Any]]**\
  Provides a list of all supported networks with their chain IDs and RPC endpoints.
* **setKeys(private\_Key: str) -> str**\
  Stores the sender's private key (linked to your current bot ID) in the MongoDB collection.
* **sendNativeCoin(...) -> str**\
  Sends native coins (like ETH) on the selected EVM chain. Features include automatic gas estimation, retry logic, and optional proxy support.
* **sendETHER(...) -> str**\
  When provided with a token contract address, this function sends tokens via the contract's transfer method. It supports the same features as sendNativeCoin.&#x20;

Additionally, the following aliases are available for token transfers:\
`send_ether`, `sendether`, and `sendEther` (all reference the same function).\
For more details please read [https://help.telebotcreator.com/crypto-libraries-documentation](https://help.telebotcreator.com/crypto-libraries-documentation).

***

## **Usage Examples**

In TPY language on Telebot Creator, you define your function alias like this:

```tpy
sendETHER = libs.web3lib.sendETHER
```

Below are several examples demonstrating how to use these functions:

#### **Example 1: Sending a Native Coin Transfer (ETH)**

```tpy
dummy_private_key = "0xYOUR_PRIVATE_KEY_HERE"
test_rpc = "https://rpc.ankr.com/eth"  # Use explicit RPC URL or specify network below
test_recipient = "0xRecipientAddressHere"

# Native transfer example (contract_address omitted)
tx_hash = libs.web3lib.sendNativeCoin(
    value = 0.5,                     # Amount in Ether
    to = test_recipient,
    rpc_url = test_rpc,
    private_key = dummy_private_key,
    network = "ethereum",            # If rpc_url is empty, default Ethereum RPC is used
    retry = True,                    # Retry once on recoverable errors
    estimate_gas = True
)

bot.sendMessage("Native Transfer TX Hash: " + tx_hash)
```

#### **Example 2: Sending an ERC‑20 Token Transfer**

```tpy
dummy_private_key = "0xYOUR_PRIVATE_KEY_HERE"
dummy_contract = "0xTokenContractAddressHere"
test_recipient = "0xRecipientAddressHere"
test_rpc = "https://rpc.ankr.com/eth"

# Token transfer example (using token transfer branch)
tx_hash = libs.web3lib.sendETHER(
    value = 1,                       # Token amount (assumes 18 decimals)
    to = test_recipient,
    rpc_url = test_rpc,
    private_key = dummy_private_key,
    contract_address = dummy_contract,  # Token contract address provided triggers token transfer logic
    network = "ethereum",
    retry = True,
    estimate_gas = True
)

bot.sendMessage("Token Transfer TX Hash: " + tx_hash)
```

#### **Example 3: Using Network Parameter Only (No Explicit RPC URL)**

```tpy
dummy_private_key = "0xYOUR_PRIVATE_KEY_HERE"
dummy_contract = "0xTokenContractAddressHere"
test_recipient = "0xRecipientAddressHere"

# Use network parameter to automatically use the default RPC for Polygon
tx_hash = libs.web3lib.sendETHER(
    value = 0.25,
    to = test_recipient,
    network = "polygon",
    private_key = dummy_private_key,
    contract_address = dummy_contract,
    retry = False,
    estimate_gas = True
)

bot.sendMessage("Token Transfer on Polygon TX Hash: " + tx_hash)
```

***

**Summary:**

* This library supports 31 EVM networks with default RPC endpoints.
* It offers robust functionality with automatic gas estimation, retry logic, and proxy support.
* Aliases like `send_ether`, `sendether`, and `sendEther` are provided for convenience.
* It is published on Telebot Creator and written in TPY language.

#### **5.3 Example Use Cases for Libraries**

Here are some real-world scenarios where these libraries can be applied:

**Payment Automation**

Automate payments for subscriptions or services using payment libraries.&#x20;

*   **Example**:

    ```python
    libs.Paytm.setKeys("merchant_key", "merchant_id", "auth_token")
    libs.Paytm.send(500, "1234567890", "Subscription payment")
    ```

**User Data Tracking**

Manage user data for referral systems or leaderboards with the CSV library.

*   **Example**:

    ```python
    csv = libs.CSV.CSVHandler("referrals.csv")
    csv.add_row({"User": "Bob", "Referrals": 5})
    ```

**Crypto Payment Bots**

Handle cryptocurrency transactions for services or rewards using blockchain libraries.

*   **Example**:

    ```python
    libs.Polygon.setKeys("your_private_key")
    libs.Polygon.sendPolygon(2.0, "recipient_wallet_address")
    ```

**Random Giveaways**

Use the Random library to create giveaways or dynamic responses.

*   **Example**:

    ```python
    random_number = libs.Random.randomInt(1, 100)
    bot.sendMessage(f"Your random number is: {random_number}")
    ```

**Webhook Integrations**

Connect your bot with external services using the Webhook library.

*   **Example**:

    ```python
    webhook_url = libs.Webhook.getUrlFor("process_payment", user_id=12345)
    bot.sendMessage(f"Webhook URL: {webhook_url}")
    ```

**13. libs.openai_lib**

Provides a client for interacting with OpenAI's API, enabling AI-powered features in your bot.

* **Classes**:
  * `OpenAIClient`: Core client for interacting with OpenAI API.
  * `AIAssistant`: Higher-level class for working with OpenAI Assistants.
* **Error Classes**:
  * `OpenAIError`: Base exception class for OpenAI errors.
  * `OpenAITimeoutError`: Error for request timeouts.
  * `OpenAIAPIError`: Error for API-specific issues.
* **Key Methods in OpenAIClient**:
  * `create_chat_completion`: Generates text completions using OpenAI models.
  * `create_assistant`: Creates a new OpenAI Assistant.
  * `create_thread`: Creates a new conversation thread.
  * `create_message`: Adds a message to a conversation thread.
  * `create_run`: Runs an assistant on a thread to generate a response.
* **Key Methods in AIAssistant**:
  * `start_conversation`: Starts a new conversation thread.
  * `send_message`: Sends a message and returns the assistant's response.
  * `get_conversation_history`: Retrieves the history of messages in a thread.

*   **Example - Chat Completion**:

    ```python
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

*   **Example - Using Assistant**:

    ```python
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

**14. libs.gemini_lib**

Provides a client for interacting with Google's Gemini AI models, offering an OpenAI-compatible interface.

* **Classes**:
  * `GeminiClient`: Core client for interacting with Gemini API.
  * `GeminiAIAssistant`: Higher-level class for working with Gemini in an assistant-like way.
* **Error Classes**:
  * `GeminiError`: Base exception class for Gemini errors.
  * `GeminiTimeoutError`: Error for request timeouts.
  * `GeminiAPIError`: Error for API-specific issues.
* **Key Methods in GeminiClient**:
  * `create_chat_completion`: Generates text completions using Gemini models.
  * `create_assistant`: Creates a new assistant-like interface.
  * `create_thread`: Creates a new conversation thread.
  * `create_message`: Adds a message to a conversation thread.
  * `create_run`: Runs an assistant on a thread to generate a response.
* **Key Methods in GeminiAIAssistant**:
  * `start_conversation`: Starts a new conversation thread.
  * `send_message`: Sends a message and returns the assistant's response.
  * `get_conversation_history`: Retrieves the history of messages in a thread.

*   **Example - Chat Completion**:

    ```python
    client = libs.gemini_lib.GeminiClient(api_key="YOUR_API_KEY")
    response = client.create_chat_completion(
        model="gemini-2.0-flash",
        messages=[
            {"role": "user", "content": "What are the best practices for Telegram bot development?"}
        ]
    )
    bot.sendMessage(response["choices"][0]["message"]["content"])
    ```

*   **Example - Using Assistant**:

    ```python
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

#### **5.4 Summary of Libraries**

| **Library**          | **Purpose**                                                       | **Key Functions**                                                   |
| -------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------- |
| **libs.Resources**   | Managing user and global resources                                | `value`, `add`, `cut`, `set`, `reset`, `getAllData`                 |
| **libs.Coinbase**    | Coinbase payment integration                                      | `setKeys`, `post`                                                   |
| **libs.Crypto**      | Cryptocurrency conversions and pricing                            | `convert`, `get_price`                                              |
| **libs.Random**      | Generating random numbers and strings                             | `randomInt`, `randomStr`, `randomFloat`, `randomAscii`              |
| **libs.Paytm**       | Paytm payment integration                                         | `setKeys`, `send`                                                   |
| **libs.CSV**         | Data management with CSV files                                    | `create_csv`, `add_row`, `edit_row`, `get`, `delete`                |
| **libs.Webhook**     | Creating and managing webhooks                                    | `genRandomId`, `getUrlFor`                                          |
| **libs.DateAndTime** | Working with dates and times                                      | `utcnow`, `date_now`, `time`, `now`                                 |
| **libs.Oxapay**      | Oxapay payment integration                                        | `post`                                                              |
| **libs.customHTTP**  | Performing HTTP requests with constraints                         | `get`, `post`, `put`, `delete`, `head`, `options`, `patch`, `close` |
| **libs.web3lib**     | Ethereum-compatible blockchain transactions (Supports EVM chains) | `setKeys`, `sendETHER`, `sendNativeCoin`                            |
| **libs.openai_lib**  | OpenAI API integration for AI capabilities                        | `OpenAIClient`, `AIAssistant`, create_chat_completion                |
| **libs.gemini_lib**  | Google Gemini API integration for AI capabilities                 | `GeminiClient`, `GeminiAIAssistant`, create_chat_completion         |

#### **5.6 Real-World Applications of Libraries**

Here are some practical examples of how you can use these libraries in your bots:

**Crypto Payment Bots**

Handle cryptocurrency payments for services or rewards.

*   **Example**:

    ```python
    dummy_private_key = "0xYOUR_PRIVATE_KEY_HERE"
    dummy_contract = "0xTokenContractAddressHere"
    test_recipient = "0xRecipientAddressHere"

    # Use network parameter to automatically use the default RPC for Polygon
    tx_hash = libs.web3lib.sendETHER(
        value = 0.25,
        to = test_recipient,
        network = "polygon",
        private_key = dummy_private_key,
        contract_address = dummy_contract,
        retry = False,
        estimate_gas = True
    )

    bot.sendMessage("Token Transfer on Polygon TX Hash: " + tx_hash)
    ```

**Referral and Loyalty Systems**

Manage user points or rewards for referrals and other actions.

*   **Example**:

    ```python
    points = libs.Resources.userRes("points", user)
    points.add(10)
    current_points = points.value()
    bot.sendMessage(f"You now have {current_points} points!")
    ```

**Data-Driven Decisions**

Log and analyze user data or create leaderboards using CSV files.

*   **Example**:

    ```python
    csv = libs.CSV.CSVHandler("leaderboard.csv")
    csv.add_row({"User": "Charlie", "Score": 250})
    ```

**Automated Payments**

Use Paytm or Coinbase libraries to automate payment transfers.

*   **Example**:

    ```python
    libs.Paytm.setKeys("merchant_key", "merchant_id", "auth_token")
    libs.Paytm.send(100, "9876543210", "Payment for services")
    ```

**Random Giveaways**

Create random giveaways or generate unique codes.

*   **Example**:

    ```python
    random_number = libs.Random.randomInt(1, 100)
    bot.sendMessage(f"Your random number is: {random_number}")
    ```

**OpenAI Integration**

Use the OpenAI library to add AI capabilities to your bot.

*   **Example - Chat Completion**:

    ```python
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

**Gemini Integration**

Use the Gemini library to add AI capabilities to your bot.

*   **Example - Chat Completion**:

    ```python
    client = libs.gemini_lib.GeminiClient(api_key="YOUR_API_KEY")
    response = client.create_chat_completion(
        model="gemini-2.0-flash",
        messages=[
            {"role": "user", "content": "What are the best practices for Telegram bot development?"}
        ]
    )
    bot.sendMessage(response["choices"][0]["message"]["content"])
    ```

***

By using these libraries, you can significantly extend the capabilities of your Telegram bots, making them more interactive, efficient, and feature-rich. Whether you need to handle payments, manage data, or integrate with blockchain technologies, TBC's libraries provide the tools you need to build powerful bots with ease.
