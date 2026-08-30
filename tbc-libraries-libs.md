# TBC Libraries (Libs)

*Telebot Creator Documentation — Platform v7.1.2 · Telegram Bot API 10.1*

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
>
> **New in 5.0.0**:
> 
> - Commands can now run up to 160 seconds (increased from 120 seconds)
> - New stats tracking capabilities in Account and Bot classes
> - Data transfer functionality between bots
> - OpenRouter API support in openai_lib with extended timeouts
>
> **New in 7.1.2**:
>
> - New `libs.security` library — HMAC, Ed25519 verification, AES encryption, hashing
> - Webhook commands now receive `options.headers` (HTTP headers) and `options.ip` (caller IP)
> - Full backward compatibility — existing bot code is not affected

#### **5.1 Overview of Libraries**

TBC libraries are grouped based on their functionalities to help you develop bots efficiently:

* **Payment Integrations**: Automate payments using popular services.
* **Blockchain Transactions**: Manage cryptocurrency transfers and transactions.
* **Data Handling**: Work with data easily using CSV files.
* **Randomization**: Generate random numbers or strings for games and giveaways.
* **Resource Management**: Track points, credits, or other resources for users or globally.


**7. libs.PremiumGift**

Sends Telegram Premium **gifts** (paid for in Telegram Stars) to users via the bot's own token. The library ships with a built-in `GIFTS` catalog organised into **Budget** (15–25 Stars), **Premium** (50 Stars) and **Luxury** (100 Stars) tiers, and records every send to the bot's gift history for stats and auditing.

* **Catalog**:
  * `GIFTS`: The built-in catalog dictionary (keys like `"heart"`, `"rose"`, `"diamond"`, each with `name`, `id`, `stars`, `category`, `emoji`, `description`).
  * `get_gift_catalog(category=None)`: Returns the catalog as a list, optionally filtered by `"Budget"`, `"Premium"` or `"Luxury"`.
  * `get_gift_by_key(gift_key)` / `get_gift_by_id(gift_id)`: Look up a single gift.
  * `get_gifts_by_category()`: Returns gifts grouped by category.
  * `search_gifts(query)`: Search by name, description or emoji.
  * `get_gift_price_range()`: Min/max/average Star prices.
* **Sending**:
  * `send_gift(user_id, gift_key, message=None, bot_token=None)`: Sends a catalog gift (by key) to `user_id`. Uses the current bot's token unless `bot_token` is supplied. Returns a dict with `ok` plus details.
  * `send_gift_by_id(user_id, gift_id, message=None, bot_token=None)`: Sends a gift by its raw Telegram gift ID (works even for gifts not in the catalog).
* **History & Stats**:
  * `get_gift_history(limit=50, status=None, recipient_id=None)`: Recent sends for the current bot.
  * `get_gift_stats()`: Aggregate counts and total Stars sent.
* **Settings**:
  * `set_gift_setting(key, value)`, `get_gift_setting(key, default=None)`, `get_all_gift_settings()`.

> **Note**: The recipient must allow gifts from your bot, and the bot's Stars balance must cover the gift price. Sending uses Telegram's `sendGift` Bot API method under the hood.

*   **Example**:

    ```python
    # Show the budget catalog
    for gift in libs.PremiumGift.get_gift_catalog("Budget"):
        bot.sendMessage(f"{gift['name']} — {gift['stars']} Stars ({gift['gift_key']})")

    # Send a rose to the current user
    result = libs.PremiumGift.send_gift(int(user), "rose", message="Thanks for playing!")
    if result["ok"]:
        bot.sendMessage("Gift sent! 🌹")
    else:
        bot.sendMessage(f"Could not send gift: {result['error']}")
    ```

**8. libs.MDxchange**

Integrates with the **MDxchange** payment/automation API for crypto deposits, payouts and Telegram Stars / Premium purchases.

* **Functions**:
  * `set_api_key(api_key)`: Stores your MDxchange API key for the current bot. Returns `True`/`False`.
  * `get_api_key()`: Returns the stored API key (or `None`).
  * `deposit(currency, callback_url=None, api_key=None)`: Creates a deposit request and returns the API response (address/details).
  * `payout(currency, amount, address, description="", callback_url=None, api_key=None)`: Sends a crypto payout to `address`.
  * `buy_stars(username, currency, amount, callback_url=None, api_key=None)`: Buys Telegram Stars for a username (`amount` must be at least `50`).
  * `buy_premium(username, currency, duration, callback_url=None, api_key=None)`: Buys Telegram Premium for a username (`duration` must be `3`, `6` or `12` months).

*   **Example**:

    ```python
    libs.MDxchange.set_api_key("YOUR_MDXCHANGE_API_KEY")

    # Create a deposit address for USDT
    deposit = libs.MDxchange.deposit("USDT", callback_url=libs.Webhook.getUrlFor("mdx_ipn", user_id=user))
    bot.sendMessage(f"Send USDT to: {deposit}")

    # Gift 100 Stars to a user
    libs.MDxchange.buy_stars(username="someuser", currency="USDT", amount=100)
    ```

**9. libs.Coinpayments**

Thin wrapper around the **CoinPayments** API for crypto payment processing.

* **Functions**:
  * `setKeys(public_key, private_key)`: Stores your CoinPayments API credentials for the current bot. Returns `{"ok": "success"}`.
  * `post(public_key=None, private_key=None)`: Returns a configured `CoinPaymentsAPI` client. If keys are omitted, the stored credentials are used.

*   **Example**:

    ```python
    libs.Coinpayments.setKeys("YOUR_PUBLIC_KEY", "YOUR_PRIVATE_KEY")
    client = libs.Coinpayments.post()
    # client is a CoinPaymentsAPI instance — call its methods as documented by CoinPayments
    ```

> **Note**: The returned client is an instance of `CoinPaymentsAPI` from the external `coinpayments` package; its methods are defined by that library, not by Telebot Creator.

**10. libs.Webhook**

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
   * **In webhook commands**, the incoming response data is stored in `options`, not `message`. The `options` object includes the following keys:
     * **`data`**: The raw incoming data.
     * **`json`**: A parsed JSON object from the webhook payload.
     * **`headers`**: *(New in 7.1.2)* A dictionary of HTTP request headers (e.g., `options.headers.get("X-Coinsway-Signature", "")`).
     * **`ip`**: *(New in 7.1.2)* The IP address of the client that called the webhook.
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

**11. libs.DateAndTime**

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

**12. libs.Oxapay**

Integrates the Oxapay payment system for merchants.

* **Functions**:
  * `post(merchant_api_key)`: Initializes the Oxapay client with your API key.
*   **Example**:

    ```python
    client = libs.Oxapay.post("your_merchant_api_key")
    bot.sendMessage("Oxapay client initialized!")
    ```

**13. libs.customHTTP**

Performs HTTP requests with built-in size and timeout limits, SSRF protection, and rotating proxy support. The platform already exposes a ready-made `HTTP` object built on this class, but you can instantiate your own client when you need custom settings.

The defaults are:

* **Default timeout**: `60` seconds (`default_timeout=60`).
* **Maximum timeout**: `120` seconds (`max_timeout=120`). Requests exceeding this are rejected unless you pass a `fallback_command` (see below).
* **Maximum response size**: `50 MB` (`max_data_size`).

> **Note**: As of version 4.9.0, it's recommended to use the standard `HTTP` module for most requests; instantiate `libs.customHTTP()` only when you need a separate client with custom options.

* **Class**:
  * `CustomHTTP(default_timeout=60, max_data_size=52428800, use_proxy=True)`: Creates an HTTP client. (`52428800` = 50 MB.)
* **Methods**:
  * `get(url, **kwargs)`: Performs a GET request.
  * `post(url, **kwargs)`: Performs a POST request.
  * `put(url, **kwargs)`: Performs a PUT request.
  * `delete(url, **kwargs)`: Performs a DELETE request.
  * `head(url, **kwargs)`: Performs a HEAD request.
  * `options(url, **kwargs)`: Performs an OPTIONS request.
  * `patch(url, **kwargs)`: Performs a PATCH request.
  * `close()`: No-op kept for API compatibility (the shared `requests` session is reused).

**`fallback_command` (long-running requests):** Any request method accepts a `fallback_command` keyword. If the requested `timeout` exceeds the 120-second maximum, the request is **offloaded to a background worker** and the response is delivered to the named TBC command via a webhook when it completes. The call then returns immediately with a `{"task_id", "status": "processing", "message": ...}` dict instead of the response. Without `fallback_command`, a timeout over 120 s raises an error.

> **Security**: `customHTTP` only allows `http`/`https` URLs and blocks requests that resolve to internal, loopback, reserved or cloud-metadata addresses (SSRF guard), as well as `.gov` domains.

*   **Example — simple request**:

    ```python
    http_client = libs.customHTTP()
    response = http_client.get("https://api.example.com/data")
    bot.sendMessage(f"Response: {response.text}")
    # No need to close — the session is reused for future requests
    ```

*   **Example — offload a slow request**:

    ```python
    # This request may take longer than 120s, so hand it to a webhook command.
    http_client = libs.customHTTP()
    job = http_client.get(
        "https://slow.example.com/big-report",
        timeout=300,
        fallback_command="handle_report_result"
    )
    bot.sendMessage(f"Report queued: {job['task_id']}")
    # The /handle_report_result command receives the response via options.* later.
    ```

**14. TonLib**

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
  * `register_ton_connect_wallet(session_id, wallet_address)`: Marks a session as connected and stores the connected wallet address (used by your callback after the user approves).
  * `create_ton_connect_payload(callback_url, items=None, return_url=None)`: Builds a raw TON Connect payload / deep link for custom request flows.
  * `request_ton_transaction(to_address, amount, comment=None, callback_url="", return_url=None)`: Requests a TON transfer from a connected wallet.

* **Jetton Operations**:
  * `get_jetton_metadata(jetton_master_address, api_key=None, endpoint=None)`: Retrieves information about a Jetton (token).
  * `get_jetton_wallet_address(owner_address, jetton_master_address, api_key=None, endpoint=None)`: Resolves the Jetton wallet address for an owner on a given Jetton master.
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

**15. libs.web3lib**

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

* **get\_supported\_networks() -> Dict\[str, Dict\[str, Any]]**\
  Provides a list of all supported networks with their chain IDs and RPC endpoints.
* **setKeys(private\_Key: str) -> str**\
  Stores the sender's private key (linked to your current bot ID) in the MongoDB collection.
* **sendNativeCoin(...) -> str**\
  Sends native coins (like ETH) on the selected EVM chain. Features include automatic gas estimation, retry logic, and optional proxy support.
* **sendETHER(..., decimals=None) -> str**\
  When provided with a token contract address, this function sends ERC‑20 tokens via the contract's transfer method. It supports the same features as sendNativeCoin. Tokens are assumed to have 18 decimals; for tokens that use a different precision (e.g. USDT/USDC with 6 decimals) pass `decimals=6`. Valid range is `0`–`18`.
* **getBalance(address, rpc_url=None, network=None, unit="ether") -> float**\
  Returns the native coin balance of an address. `unit` may be `"wei"`, `"gwei"` or `"ether"`. Alias: `get_balance`.
* **getTokenBalance(address, contract_address, rpc_url=None, network=None, decimals=None, raw=False) -> float**\
  Returns the ERC‑20 token balance of an address. `decimals` is auto-detected from the contract when omitted (falling back to 18); pass `raw=True` to get the unscaled on-chain integer. Alias: `get_token_balance`.

Additionally, the following aliases are available for token transfers:\
`send_ether`, `sendether`, and `sendEther` (all reference the same function as `sendETHER`).\
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
    libs.Coinpayments.setKeys("merchant_public_key", "merchant_private_key")
    client = libs.Coinpayments.post()
    # Use the client to create a transaction / check balances
    ```

**User Data Tracking**

Manage user data for referral systems or leaderboards with the CSV library.

*   **Example**:

    ```python
    csv = libs.CSV.CSVHandler("referrals.csv")
    csv.add_row({"User": "Bob", "Referrals": 5})
    ```

**Crypto Payment Bots**

Handle cryptocurrency transactions for services or rewards using `libs.web3lib` (all EVM chains).

*   **Example**:

    ```python
    tx_hash = libs.web3lib.sendETHER(
        value=2.0,
        to="recipient_wallet_address",
        private_key="your_private_key",
        network="polygon"
    )
    bot.sendMessage(f"TX: {tx_hash}")
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

**16. libs.openai_lib**

Provides a client for interacting with OpenAI's API and OpenRouter API, enabling AI-powered features in your bot.

* **Classes**:
  * `OpenAIClient`: Core client for interacting with OpenAI API or OpenRouter API.
  * `AIAssistant`: Higher-level class for working with OpenAI Assistants or direct chat completions.
* **Error Classes**:
  * `OpenAIError`: Base exception class for OpenAI errors.
  * `OpenAITimeoutError`: Error for request timeouts.
  * `OpenAIAPIError`: Error for API-specific issues.
* **Key Methods in OpenAIClient**:
  * `create_chat_completion`: Generates text completions using OpenAI or OpenRouter models.
  * `create_assistant`: Creates a new OpenAI Assistant (OpenAI API only).
  * `create_thread`: Creates a new conversation thread (OpenAI API only).
  * `create_message`: Adds a message to a conversation thread (OpenAI API only).
  * `create_run`: Runs an assistant on a thread to generate a response (OpenAI API only).
* **Key Methods in AIAssistant**:
  * `start_conversation`: Starts a new conversation thread.
  * `send_message`: Sends a message and returns the assistant's response.
  * `get_conversation_history`: Retrieves the history of messages in a thread.
* **New in 5.0.0**:
  * Extended timeout support up to 160 seconds
  * OpenRouter API integration with access to various models like Llama
  * Enhanced error handling and retries

*   **Example - Chat Completion with OpenAI**:

    ```python
    client = libs.openai_lib.OpenAIClient(api_key="YOUR_API_KEY", timeout=120)
    response = client.create_chat_completion(
        model="gpt-4o",
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": "Tell me about Telegram bots."}
        ]
    )
    bot.sendMessage(response["choices"][0]["message"]["content"])
    ```

*   **Example - Using OpenAI Assistant**:

    ```python
    client = libs.openai_lib.OpenAIClient(api_key="YOUR_API_KEY", timeout=120)
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
    
*   **Example - Using OpenRouter API**:

    ```python
    # Initialize with OpenRouter API key and extended timeout
    client = libs.openai_lib.OpenAIClient(
        api_key="YOUR_OPENROUTER_API_KEY",
        timeout=120,
        base_url="https://openrouter.ai/api/v1"
    )
    
    # Use with a specific model via AIAssistant
    assistant = libs.openai_lib.AIAssistant(
        openai_client=client,
        model="meta-llama/llama-3.3-8b-instruct:free",
        system_message="You're a helpful assistant."
    )
    
    # Send message and get response
    response = assistant.send_message("Tell me about Telegram bots")
    response_text = str(response.get("content")[0]['text']['value'])
    bot.sendMessage(response_text)
    ```

**17. libs.gemini_lib**

Provides a client for interacting with Google's Gemini AI models, offering an OpenAI-compatible interface.

> **Note**: The Gemini client caps every request at a **40-second timeout** (`MAX_TIMEOUT = 40`). Any larger `timeout` value you pass is clamped down to 40 seconds.

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

***

**libs.Random**

Utility functions for generating random values — handy for games, giveaways, codes and tokens. All functions are also available in lowercase (e.g. `randomint`, `randomchoice`).

* **Numbers**:
  * `randomInt(min, max)`: Random integer in `[min, max]` (inclusive). Alias: `randomInteger`.
  * `randomFloat(min, max)`: Random float in `[min, max]`.
  * `randomRange(start, stop, count)`: `count` **unique** integers sampled from `[start, stop]`.
  * `randomGaussian(mean, stddev)`: Random float from a Gaussian (normal) distribution.
* **Strings & bytes**:
  * `randomStr(length, charSet=None)`: Random alphanumeric string (or from a custom `charSet`). Alias: `randomString`.
  * `randomAscii(length)`: Random string of ASCII letters.
  * `randomHex(length)`: Random hex string of the given length.
  * `randomBytes(length)`: Random `bytes` object.
  * `randomUUID()`: A random UUID4 string.
  * `randomPassword(length, use_digits=True, use_specials=True)`: Random password with optional digits and special characters.
* **Collections**:
  * `randomChoice(data)`: One random element from `data`.
  * `randomSample(data, k)`: `k` unique random elements from `data`.
  * `randomShuffle(data)`: A new shuffled copy of `data` (original is left unchanged).
  * `randomWeightedChoice(data, weights)`: One element chosen according to `weights`.
* **Misc**:
  * `randomBoolean()`: Random `True`/`False`.

*   **Example**:

    ```python
    code = libs.Random.randomStr(8)                       # e.g. "Ax7Qz1Pp"
    winner = libs.Random.randomChoice(["Alice", "Bob", "Cara"])
    prize = libs.Random.randomWeightedChoice([10, 50, 100], weights=[70, 25, 5])
    token = libs.Random.randomHex(16)
    bot.sendMessage(f"Winner: {winner} — prize {prize} (code {code})")
    ```

#### **5.4 Summary of Libraries**

| **Library**          | **Purpose**                                                       | **Key Functions**                                                   |
| -------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------- |
| **libs.Resources**   | Managing user, global and account resources                       | `value`, `add`, `cut`, `set`, `reset`, `getAllData`, `fetchAllResources`, `fetchAllResourcesOfUser`, `deleteSingleUserData`, `deleteAllUsersData`, `clearAllUserOrAccountData` |
| **libs.Coinbase**    | Coinbase payment integration                                      | `setKeys`, `post`                                                   |
| **libs.Coinpayments**| CoinPayments crypto payment integration                           | `setKeys`, `post`                                                   |
| **libs.MDxchange**   | MDxchange deposits, payouts, Stars & Premium                      | `set_api_key`, `deposit`, `payout`, `buy_stars`, `buy_premium`      |
| **libs.PremiumGift** | Sending Telegram Stars premium gifts                              | `send_gift`, `send_gift_by_id`, `get_gift_catalog`, `get_gift_history`, `get_gift_stats`, `GIFTS` |
| **libs.Crypto**      | Cryptocurrency conversions and pricing                            | `convert`, `get_price`, `fetch_price`, `get_coin_info`              |
| **libs.Random**      | Generating random numbers, strings and more                       | `randomInt`, `randomStr`, `randomFloat`, `randomAscii`, `randomChoice`, `randomShuffle`, `randomSample`, `randomBoolean`, `randomHex`, `randomBytes`, `randomUUID`, `randomRange`, `randomGaussian`, `randomWeightedChoice`, `randomPassword` |
| **libs.CSV**         | Data management with CSV files                                    | `create_csv`, `add_row`, `edit_row`, `get`, `delete`                |
| **libs.Webhook**     | Creating and managing webhooks                                    | `genRandomId`, `getUrlFor`                                          |
| **libs.DateAndTime** | Working with dates and times                                      | `utcnow`, `date_now`, `time`, `now`                                 |
| **libs.Oxapay**      | Oxapay payment integration                                        | `post`                                                              |
| **libs.customHTTP**  | Performing HTTP requests with constraints                         | `get`, `post`, `put`, `delete`, `head`, `options`, `patch`, `close` |
| **libs.TonLib**      | TON blockchain, jettons and TON Connect                           | `generateWallet`, `getBalance`, `sendTON`, `get_jetton_balance`, `create_ton_connect_session` |
| **libs.web3lib**     | EVM-compatible blockchain transactions & balances                 | `setKeys`, `sendETHER`, `sendNativeCoin`, `getBalance`, `getTokenBalance`, `get_supported_networks` |
| **libs.OpenCV**      | Image processing (OpenCV/NumPy)                                   | `read_image_from_bytes`, `resize`, `detect_faces`, `apply_filter`, `to_bytes` |
| **libs.Pillow**      | Image editing (PIL)                                              | `open_from_bytes`, `resize`, `draw_text`, `add_watermark`, `to_bytes` |
| **libs.openai_lib**  | OpenAI API integration for AI capabilities                        | `OpenAIClient`, `AIAssistant`, create_chat_completion                |
| **libs.gemini_lib**  | Google Gemini API integration for AI capabilities                 | `GeminiClient`, `GeminiAIAssistant`, create_chat_completion         |
| **libs.security**    | Cryptographic toolkit — HMAC, Ed25519, AES, hashing              | `hmac_sign`, `hmac_verify`, `ed25519_verify`, `encrypt`, `decrypt`  |

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

Use Coinbase, Coinpayments, Oxapay or MDxchange libraries to automate payment transfers.

*   **Example**:

    ```python
    libs.Coinbase.setKeys("YOUR_API_KEY", "YOUR_API_SECRET")
    client = libs.Coinbase.post()
    balances = client.getBalance()
    bot.sendMessage(f"Wallet balances: {balances}")
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

**18. libs.security**

> **New in 7.1.2**

A cryptographic toolkit providing HMAC signatures, Ed25519 asymmetric signature verification, AES encryption/decryption, and hashing functions.

* **HMAC (Symmetric Signatures)**:
  * `hmac_sign(secret, data, algorithm="sha256")`: Create an HMAC signature. Returns hex string.
  * `hmac_verify(secret, data, signature, algorithm="sha256")`: Verify an HMAC signature (timing-safe). Returns `True`/`False`.

* **Ed25519 (Asymmetric Verification)**:
  * `ed25519_verify(public_key_hex, signature_hex, data)`: Verify an Ed25519 signature using a public key. Returns `True`/`False`.
  * `verify_coinsway_webhook(public_key_hex, signature_hex, raw_body)`: Convenience method for verifying Coinsway webhook signatures.

* **AES Encryption (Fernet)**:
  * `generate_key()`: Generate a new Fernet encryption key.
  * `encrypt(plaintext, key)`: Encrypt a string. Returns encrypted token.
  * `decrypt(token, key)`: Decrypt a Fernet token. Returns plaintext.

* **Hashing**:
  * `sha256(data)`: SHA-256 hash. Returns hex string.
  * `sha512(data)`: SHA-512 hash. Returns hex string.
  * `md5(data)`: MD5 hash. Returns hex string.

*   **Example — Verify a webhook signature**:

    ```python
    sig = options.headers.get("X-Coinsway-Signature", "")
    public_key = "300f4313f53eb2a1a5c418bf44bae1d50596f3eed7ca5428e57cea40223bb1ed"
    
    if libs.security.verify_coinsway_webhook(public_key, sig, options.data):
        data = options.json
        bot.sendMessage(f"Verified deposit: {data.get('amount_human')} USDT")
    else:
        Api.setWebhookResult({"ok": False, "error": "Invalid signature"})
    ```

*   **Example — Encrypt user data**:

    ```python
    # Store ENCRYPTION_KEY in your .env command
    encrypted = libs.security.encrypt("user secret data", ENCRYPTION_KEY)
    User.saveData("encrypted_data", encrypted)
    
    # Later, decrypt it
    stored = User.getData("encrypted_data")
    decrypted = libs.security.decrypt(stored, ENCRYPTION_KEY)
    bot.sendMessage(f"Your data: {decrypted}")
    ```

*   **Example — HMAC webhook verification**:

    ```python
    secret = "my_shared_secret"
    sig = options.headers.get("X-Signature", "")
    
    if libs.security.hmac_verify(secret, options.data, sig):
        bot.sendMessage("HMAC verified!")
    else:
        bot.sendMessage("Invalid HMAC signature")
    ```

For detailed documentation, see [Version 7.1.2 Update](version-7.1.2-update.md).

***

**19. libs.OpenCV**

An image-processing toolkit built on **OpenCV (cv2)** and **NumPy**. Images are handled as NumPy arrays: read incoming photo bytes with `read_image_from_bytes`, process them, then convert back to bytes with `to_bytes` before sending. (Telebot Creator sandboxes the filesystem, so always work with in-memory bytes — never local file paths.)

* **I/O**:
  * `read_image_from_bytes(image_bytes)`: Decode raw bytes into an image array.
  * `to_bytes(image, format=".jpg", params=None)`: Encode an image array to a `BytesIO` buffer.
  * `create_blank(width, height, color=(255, 255, 255))`: Create a blank canvas.
* **Geometry & color**:
  * `resize(image, width=None, height=None)`, `rotate(image, angle)`, `perspective_transform(image, src_points, dst_points)`.
  * `convert_color(image, conversion_type)`, `threshold(image, thresh_value=127, max_value=255, ...)`, `adaptive_threshold(image, max_value=255, ...)`.
* **Filters & effects**:
  * `apply_filter(image, filter_type)`, `detect_edges(image, threshold1=100, threshold2=200)`, `morph_operations(image, operation, kernel_size=5)`, `blend_images(image1, image2, alpha=0.5)`.
* **Drawing**:
  * `draw_text(image, text, position, ...)`, `draw_rectangle(image, pt1, pt2, ...)`, `find_contours(image, ...)`, `draw_contours(image, contours, ...)`.
* **Faces**:
  * `detect_faces(image)`: Returns a list of `(x, y, w, h)` face boxes.
  * `draw_faces(image, faces, ...)`: Draws boxes around detected faces.

*   **Example — resize a photo the user sent**:

    ```python
    file_info = bot.getFile(message.photo[-1].file_id)
    raw = HTTP.get(f"https://api.telegram.org/file/bot{bot_token}/{file_info.file_path}").content

    img = libs.OpenCV.read_image_from_bytes(raw)
    small = libs.OpenCV.resize(img, width=320)
    out = libs.OpenCV.to_bytes(small, format=".jpg")
    bot.sendPhoto(out)
    ```

*   **Example — detect and box faces**:

    ```python
    img = libs.OpenCV.read_image_from_bytes(raw)
    faces = libs.OpenCV.detect_faces(img)
    boxed = libs.OpenCV.draw_faces(img, faces)
    bot.sendPhoto(libs.OpenCV.to_bytes(boxed, format=".png"))
    bot.sendMessage(f"Found {len(faces)} face(s)")
    ```

***

**20. libs.Pillow**

An image-editing toolkit built on **Pillow (PIL)**. Like `libs.OpenCV`, it works entirely with in-memory bytes: `open_from_bytes` to load and `to_bytes` to export.

* **I/O**:
  * `open_from_bytes(image_bytes)`: Load an image from raw bytes.
  * `to_bytes(image, format="JPEG", **params)`: Export an image to a `BytesIO` buffer.
  * `create_image(width, height, color=(255, 255, 255))`, `create_gradient(width, height, start_color, end_color, ...)`.
* **Transform**:
  * `resize(image, width=None, height=None, resample=...)`, `crop(image, box)`, `rotate(image, angle, expand=False)`, `mirror(image, direction="horizontal")`.
* **Adjustments & filters**:
  * `adjust_brightness(image, factor)`, `adjust_contrast(image, factor)`, `adjust_color(image, factor)`, `adjust_sharpness(image, factor)`, `apply_filter(image, filter_type)`, `invert(image)`.
* **Composition**:
  * `composite(image1, image2, mask=None)`, `paste(base_image, image_to_paste, ...)`, `blend(image1, image2, alpha=0.5)`, `add_border(image, border, color="black")`, `add_watermark(image, watermark, ...)`, `apply_mask(image, mask)`.
* **Drawing**:
  * `draw_text(image, text, position, ...)`, `draw_rectangle(image, xy, ...)`, `draw_circle(image, xy, ...)`, `draw_line(image, xy, ...)`.
* **Channels & modes**:
  * `convert_mode(image, mode)`, `split_channels(image)`, `merge_channels(r, g, b)`.

*   **Example — add a caption and border**:

    ```python
    img = libs.Pillow.open_from_bytes(raw)
    img = libs.Pillow.draw_text(img, "Winner!", (20, 20))
    img = libs.Pillow.add_border(img, 10, color="gold")
    bot.sendPhoto(libs.Pillow.to_bytes(img, format="PNG"))
    ```

*   **Example — build a gradient banner**:

    ```python
    banner = libs.Pillow.create_gradient(600, 200, (255, 0, 128), (0, 128, 255))
    banner = libs.Pillow.draw_text(banner, "Daily Reward", (40, 80))
    bot.sendPhoto(libs.Pillow.to_bytes(banner, format="PNG"))
    ```

***

By using these libraries, you can significantly extend the capabilities of your Telegram bots, making them more interactive, efficient, and feature-rich. Whether you need to handle payments, manage data, or integrate with blockchain technologies, TBC's libraries provide the tools you need to build powerful bots with ease.

**4. libs.Resources**

The `libs.Resources` library in Telebot Creator provides a simple and efficient way to track and manage numeric values in your bot. These can represent points, credits, scores, or any other numerical resource.

### Resource Classes

#### userRes

Create and manage resources for specific users.

```python
res = libs.Resources.userRes(name, user=None, bot_id=None, api_key=None)
```

**Parameters:**
- `name` (Required): The name of the resource.
- `user` (Optional): User ID to associate with this resource. Defaults to the current user.
- `bot_id` / `api_key` (Optional): Target another bot's resources (see **Cross-bot access** below).

#### globalRes

Create and manage global resources that apply across all users.

```python
res = libs.Resources.globalRes(name, bot_id=None, api_key=None)
```

**Parameters:**
- `name` (Required): The name of the resource.
- `bot_id` / `api_key` (Optional): Target another bot's resources (see **Cross-bot access** below).

#### anotherRes

Create and manage resources for a specific user (even if not the current user).

```python
res = libs.Resources.anotherRes(name, user, bot_id=None, api_key=None)
```

**Parameters:**
- `name` (Required): The name of the resource.
- `user` (Required): User ID to associate with this resource.
- `bot_id` / `api_key` (Optional): Target another bot's resources (see **Cross-bot access** below).

#### adminRes

Create and manage resources with administrative privileges. Required for the bulk/fetch/delete methods below.

```python
res = libs.Resources.adminRes(name, user=None, bot_id=None, api_key=None)
```

**Parameters:**
- `name` (Required): The name of the resource.
- `user` (Optional): User ID to scope the admin operations.
- `bot_id` / `api_key` (Optional): Target another bot's resources (see **Cross-bot access** below).

#### accountRes

Create and manage account-level resources that persist across all bots.

```python
res = libs.Resources.accountRes(name)
```

**Parameters:**
- `name` (Required): The name of the resource.

#### Cross-bot access (`bot_id` / `api_key`)

`userRes`, `globalRes`, `anotherRes` and `adminRes` accept optional `bot_id` and `api_key` arguments. Pass them together to read or modify the resources of **another** bot you own — `api_key` (the owner's account API key) is validated against `bot_id` before access is granted. When both are omitted, operations target the current bot.

```python
# Read a "points" resource belonging to a different bot you own
other = libs.Resources.userRes("points", user="123456789",
                               bot_id="OTHER_BOT_ID", api_key="YOUR_ACCOUNT_API_KEY")
bot.sendMessage(f"That bot shows {other.value()} points")
```

### Resource Methods

All resource classes support the value methods (`value`, `add`, `cut`, `set`, `reset`). The data-export and bulk-delete methods (`getAllData`, `fetchAllResources`, `fetchAllResourcesOfUser`, `deleteSingleUserData`, `deleteAllUsersData`, `clearAllUserOrAccountData`) require an **`adminRes`** instance (or `accountRes` for account-level data); calling them on a non-admin resource raises a `PermissionError`.

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

#### getAllData(length)

Returns the top entries for this resource, sorted by value (descending). Useful for leaderboards.

```python
admin = libs.Resources.adminRes("score")
top = admin.getAllData(10)
# Each entry: {"user": "<user_id>", "value": <float>}
```

**Parameters:**
- `length` (Required): Maximum number of entries to return.

#### fetchAllResources(output_format)

Exports **all** resource records for the bot (or the whole account, for `accountRes`) as a downloadable file. Requires `adminRes` or `accountRes`.

```python
admin = libs.Resources.adminRes("points")
file_obj = admin.fetchAllResources("csv")   # or "json"
bot.sendDocument(file_obj)
```

**Parameters:**
- `output_format` (Required): `"csv"` or `"json"`.

**Returns:** A `BytesIO` file object.

#### fetchAllResourcesOfUser(user, output_format)

Exports all of a single user's resource records as a file. Requires `adminRes`.

```python
admin = libs.Resources.adminRes("points")
file_obj = admin.fetchAllResourcesOfUser("123456789", "json")
bot.sendDocument(file_obj)
```

**Parameters:**
- `user` (Required): The user ID to export.
- `output_format` (Required): `"csv"` or `"json"`.

#### deleteSingleUserData(user, data=None, all_data=False)

Deletes one user's data. Requires `adminRes`.

```python
admin = libs.Resources.adminRes("points")
admin.deleteSingleUserData("123456789")              # delete this resource for the user
admin.deleteSingleUserData("123456789", all_data=True)  # delete ALL of the user's resources
```

**Parameters:**
- `user` (Required): The user ID.
- `data` (Optional): Resource name to delete (defaults to the instance's `name`).
- `all_data` (Optional): If `True`, delete every resource belonging to the user.

#### deleteAllUsersData(data=None, all_data=False)

Deletes a resource across **all** users of the bot. Requires `adminRes`.

```python
admin = libs.Resources.adminRes("points")
admin.deleteAllUsersData()                # delete "points" for every user
admin.deleteAllUsersData(all_data=True)   # delete ALL resources for every user (use with care)
```

**Parameters:**
- `data` (Optional): Resource name (defaults to the instance's `name`).
- `all_data` (Optional): If `True`, delete every resource for the bot.

#### clearAllUserOrAccountData(data=None)

Clears all records for a named resource (bot scope) or all account-level records (`accountRes`). Requires `adminRes` or `accountRes`.

```python
admin = libs.Resources.adminRes("points")
admin.clearAllUserOrAccountData()         # clear all "points" records for the bot
```

**Parameters:**
- `data` (Optional): Resource name (defaults to the instance's `name`).

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


## libs.translate — Auto-Translation

Reply to every user in their own language, 92 supported. Flip **Settings → Auto-Translate** for zero-code translation, or call it directly:

```python
libs.translate.setUser("ru")        # remember this user's language
libs.translate.setGlobal("ar")      # default for the whole bot
libs.translate.text("Hello!", "hi") # translate any string
```

Wallet addresses, amounts, links, @usernames and emojis are never altered. Full guide: [Auto-Translation](auto-translation.md).
