# TBC Libraries (Libs)

Libraries, often called **libs**, in Telebot Creator (TBC) enhance the basic TPY functionalities by offering pre-built modules for specific tasks. These libraries make it easier and faster to add advanced features to your Telegram bots, such as handling payments, managing blockchain transactions, working with data, generating random values, and more.\
\
**DEPRECATION NOTICE:**

The following libraries are deprecated and removed:

`libs.Polygon, libs.ARB, libs.TTcoin, libs.Tomochain.`

Please migrate to libs.web3lib (sendETHER) which now supports all EVM chains, proxy support, and enhanced functionality.

> **New in 4.9.0**: 
> 
> - Added native `time.sleep()` function with a maximum limit of 10 seconds
> - Direct HTTP module usage is now recommended over `libs.customHTTP()`
> - Code execution timeout increased from 60 to 120 seconds
> - Do not use `import` statements in your code
> - For handling inline queries and other update types, use the `/handler_<update_type>` command format

#### **5.1 Overview of Libraries**

TBC libraries are grouped based on their functionalities to help you develop bots efficiently:

* **Payment Integrations**: Automate payments using popular services.
* **Blockchain Transactions**: Manage cryptocurrency transfers and transactions.
* **Data Handling**: Work with data easily using CSV files.
* **Randomization**: Generate random numbers or strings for games and giveaways.
* **Resource Management**: Track points, credits, or other resources for users or globally.


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

> **Note**: As of version 4.9.0, it's recommended to use the standard HTTP module instead of libs.customHTTP() for better performance and reliability.

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

**12. TonLib**

> **New in 4.9.0**

Provides comprehensive integration with The Open Network (TON) blockchain. With TonLib, you can create wallets, check balances, send TON, work with jettons (TON's tokens), and integrate TON Connect for user wallet connections.

* **Wallet Management**:
  * `generateWallet()`: Creates a new TON wallet and returns its address and mnemonic phrase.
  * `setKeys(mnemonics)`: Stores a mnemonic phrase for later use.
  * `getWalletAddress(mnemonics=None)`: Retrieves the wallet address from stored keys or specified mnemonics.

* **TON Operations**:
  * `getBalance(address, api_key=None, endpoint=None)`: Checks the TON balance of an address.
  * `sendTON(to_address, amount, comment=None, mnemonics=None, api_key=None, endpoint=None, is_testnet=False)`: Sends TON to another address.
  * `checkTONTransaction(address, api_key=None, endpoint=None, limit=10)`: Gets recent transactions for an address.

* **TON Connect Integration**:
  * `create_ton_connect_session(user_id, expiry_seconds=86400)`: Creates a session for wallet connection.
  * `verify_ton_connect_session(session_id)`: Checks if a wallet has connected to the session.
  * `request_ton_transaction(to_address, amount, comment=None, callback_url="", return_url=None)`: Requests a TON transfer from a connected wallet.

* **Jetton Operations**:
  * `get_jetton_metadata(jetton_master_address, api_key=None, endpoint=None)`: Retrieves information about a Jetton (token).
  * `get_jetton_balance(owner_address, jetton_master_address, api_key=None, endpoint=None)`: Checks a Jetton balance.
  * `request_jetton_transfer(to_address, jetton_master_address, amount, comment=None, callback_url="", return_url=None)`: Requests a Jetton transfer.

* **Example**:

  ```python
  # Generate a wallet
  wallet = TonLib.generateWallet()
  address = wallet["address"]
  
  # Check balance
  balance = TonLib.getBalance(address)
  bot.sendMessage(f"Balance: {balance} TON")
  
  # Send TON
  TonLib.sendTON(
      to_address="EQD...",
      amount=0.1,
      comment="Test payment"
  )
  ```

For detailed documentation, see [TON Library Documentation](ton-library-documentation.md).

**13. libs.web3lib**

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

**14. libs.openai_lib**

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

**15. libs.gemini_lib**

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
| **libs.Resources**   | Managing user and global resources                                | `value`, `add`, `cut`, `set`, `reset`, `getAllData`, `clearAllData`, `fetchAllResourcesOfUser`, `removeDataOfUser`, `removeAllDataOfUser`, `removeAllData`                 |
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

**4. libs.Resources**

The `libs.Resources` library in Telebot Creator provides a simple and efficient way to track and manage numeric values in your bot. These can represent points, credits, scores, or any other numerical resource.

### Resource Classes

#### userRes

Create and manage resources for specific users.

```python
res = libs.Resources.userRes(name, user=None)
```

**Parameters:**
- `name` (Required): The name of the resource.
- `user` (Optional): User ID to associate with this resource. Defaults to the current user.

#### globalRes

Create and manage global resources that apply across all users.

```python
res = libs.Resources.globalRes(name)
```

**Parameters:**
- `name` (Required): The name of the resource.

#### anotherRes

Create and manage resources for a specific user (even if not the current user).

```python
res = libs.Resources.anotherRes(name, user)
```

**Parameters:**
- `name` (Required): The name of the resource.
- `user` (Required): User ID to associate with this resource.

#### adminRes

Create and manage resources with administrative privileges.

```python
res = libs.Resources.adminRes(name, user=None)
```

**Parameters:**
- `name` (Required): The name of the resource.
- `user` (Optional): User ID to scope the admin operations.

#### accountRes

Create and manage account-level resources that persist across all bots.

```python
res = libs.Resources.accountRes(name)
```

**Parameters:**
- `name` (Required): The name of the resource.

### Resource Methods

All resource classes support the following methods:

#### value()

Gets the current value of the resource.

```python
current_value = res.value()
```

**Returns:** The current numeric value of the resource.

#### add(value)

Adds the specified amount to the resource.

```python
new_value = res.add(amount)
```

**Parameters:**
- `value` (Required): The amount to add.

**Returns:** The new value after addition.

#### cut(value)

Subtracts the specified amount from the resource.

```python
new_value = res.cut(amount)
```

**Parameters:**
- `value` (Required): The amount to subtract.

**Returns:** The new value after subtraction.

#### set(value)

Sets the resource to a specific value.

```python
new_value = res.set(amount)
```

**Parameters:**
- `value` (Required): The value to set.

**Returns:** The new value.

#### reset()

Resets the resource value to zero.

```python
new_value = res.reset()
```

**Returns:** The new value (always 0).

### Examples

**User-specific resources:**
```python
# Create or access a user resource
points = libs.Resources.userRes("points")

# Add points for current user
points.add(100)

# Check current points
current = points.value()
bot.sendMessage(f"You have {current} points")

# Reset points
points.reset()
bot.sendMessage("Points reset to 0")
```

**Global resources:**
```python
# Create or access a global resource
jackpot = libs.Resources.globalRes("jackpot")

# Add to jackpot
jackpot.add(50)

# Check current jackpot
current = jackpot.value()
bot.sendMessage(f"Current jackpot: {current}")
```

**Managing another user's resources:**
```python
# Add points to a specific user
user_points = libs.Resources.anotherRes("points", "123456789")
user_points.add(25)
bot.sendMessage(f"Added 25 points to user. New total: {user_points.value()}")
```

**Administrative tasks:**
```python
# Get top scorers
admin = libs.Resources.adminRes("score")
top_scores = admin.getAllData(10)

result = "Top Scores:\n"
for entry in top_scores:
    result += f"{entry['user']}: {entry['value']}\n"
bot.sendMessage(result)
```

**Account-level resources:**
```python
# Create or access an account-level resource
subscription_points = libs.Resources.accountRes("subscription_points")

# Add points
subscription_points.add(100)

# Check current account points (accessible across all bots)
current = subscription_points.value()
bot.sendMessage(f"Account has {current} subscription points")
```
