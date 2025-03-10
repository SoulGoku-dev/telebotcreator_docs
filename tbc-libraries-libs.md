# TBC Libraries (Libs)

Libraries, often called **libs**, in Telebot Creator (TBC) enhance the basic TPY functionalities by offering pre-built modules for specific tasks. These libraries make it easier and faster to add advanced features to your Telegram bots, such as handling payments, managing blockchain transactions, working with data, generating random values, and more.

#### **5.1 Overview of Libraries**

TBC libraries are grouped based on their functionalities to help you develop bots efficiently:

* **Payment Integrations**: Automate payments using popular services.
* **Blockchain Transactions**: Manage cryptocurrency transfers and transactions.
* **Data Handling**: Work with data easily using CSV files.
* **Randomization**: Generate random numbers or strings for games and giveaways.
* **Resource Management**: Track points, credits, or other resources for users or globally.

#### **5.2 Key Libraries and Their Functions**

Here are the main libraries available in TBC, along with their key functions and examples of how to use them:

**1. libs.ARB**

Handles transactions on the Arbitrum (ARB) blockchain.

* **Functions**:
  * `setKeys(private_key)`: Sets your private key for ARB transactions.
  * `send(value, to, contract, private_key=None)`: Sends tokens via a contract.
  * `sendARB(value, to, private_key=None)`: Sends ARB cryptocurrency.
*   **Example**:

    ```python
    libs.ARB.setKeys("your_private_key")
    libs.ARB.sendARB(1.5, "recipient_wallet_address")
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

**6. libs.Polygon**

Supports transactions on the Polygon blockchain.

* **Functions**:
  * `setKeys(private_key)`: Sets your private key for Polygon transactions.
  * `send(value, to, contract, private_key=None)`: Sends tokens via a contract.
  * `sendPolygon(value, to, private_key=None)`: Sends MATIC tokens.
*   **Example**:

    ```python
    libs.Polygon.setKeys("your_private_key")
    libs.Polygon.sendPolygon(2.0, "recipient_wallet_address")
    ```

**7. libs.Resources**

Manages numeric values tied to users or globally for tracking points or credits.

* **Classes**:
  * `userRes(name, user)`: Manages resources for a specific user.
  * `globalRes(name)`: Manages global resources.
* **Methods**:
  * `value()`: Gets the current value.
  * `add(amount)`: Adds to the resource.
  * `cut(amount)`: Subtracts from the resource.
  * `set(amount)`: Sets the resource to a specific value.
  * `reset()`: Resets the resource to zero.
  * `getAllData(length)`: Retrieves top resource entries.
*   **Example**:

    ```python
    points = libs.Resources.userRes("points", user)
    points.add(10)
    current_points = points.value()
    bot.sendMessage(f"You now have {current_points} points!")
    ```

**8. libs.Paytm**

Integrates with the Paytm payment gateway for fund transfers and automated payments.

* **Functions**:
  * `setKeys(key, mid, token)`: Sets your Paytm merchant credentials.
  * `send(amount, number, description=None, key=None, mid=None, token=None)`: Sends a payment to a phone number.
*   **Example**:

    ```python
    libs.Paytm.setKeys("merchant_key", "merchant_id", "auth_token")
    libs.Paytm.send(100, "9876543210", "Payment for services")
    ```

**9. libs.TTcoin**

Handles transactions using TTcoin cryptocurrency.

* **Functions**:
  * `setKeys(private_key)`: Sets your TTcoin private key.
  * `sendTT(value, to, private_key=None)`: Sends TTcoin to an address.
*   **Example**:

    ```python
    libs.TTcoin.setKeys("your_private_key")
    libs.TTcoin.sendTT(50, "recipient_wallet_address")
    ```

**10. libs.Tomochain**

Supports transactions on the Tomochain network.

* **Functions**:
  * `setKeys(private_key)`: Sets your Tomochain private key.
  * `sendTRC20(value, to, contract, private_key=None)`: Sends TRC20 tokens.
  * `sendTomochain(value, to, private_key=None)`: Sends TOMO coins.
*   **Example**:

    ```python
    libs.Tomochain.setKeys("your_private_key")
    libs.Tomochain.sendTomochain(5, "recipient_wallet_address")
    ```

**11. libs.Webhook**

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

**12. libs.DateAndTime**

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

**13. libs.Oxapay**

Integrates the Oxapay payment system for merchants.

* **Functions**:
  * `post(merchant_api_key)`: Initializes the Oxapay client with your API key.
*   **Example**:

    ```python
    client = libs.Oxapay.post("your_merchant_api_key")
    bot.sendMessage("Oxapay client initialized!")
    ```

**14. libs.customHTTP**

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
    http_client.close()
    ```

**15. libs.web3lib**

Enables transactions on Ethereum-compatible blockchains.

* **Functions**:
  * `setKeys(private_key)`: Sets your private key for web3 transactions.
  * `sendETHER(value, to, contract, rpc_url, gasPrice=None, private_key=None)`: Sends tokens on an Ethereum network.
  * `sendPolygon(value, to, private_key=None)`: Sends MATIC tokens.
*   **Example**:

    ```python
    libs.web3lib.setKeys("your_private_key")
    libs.web3lib.sendETHER(1, "recipient_wallet_address", "contract_address", "https://rpc_url")
    ```

#### **5.3 Example Use Cases for Libraries**

Here are some real-world scenarios where these libraries can be applied:

**Payment Automation**

Automate payments for subscriptions or services using payment libraries.

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

#### **5.4 Additional Libraries**

Here are the remaining libraries available in TBC:

**libs.TTcoin**

Handles TTcoin cryptocurrency transactions.

* **Functions**:
  * `setKeys(private_key)`: Sets your TTcoin private key.
  * `sendTT(value, to, private_key=None)`: Sends TTcoin to an address.
*   **Example**:

    ```python
    libs.TTcoin.setKeys("your_private_key")
    libs.TTcoin.sendTT(50, "recipient_wallet_address")
    ```

**libs.Tomochain**

Supports Tomochain network transactions.

* **Functions**:
  * `setKeys(private_key)`: Sets your Tomochain private key.
  * `sendTRC20(value, to, contract, private_key=None)`: Sends TRC20 tokens.
  * `sendTomochain(value, to, private_key=None)`: Sends TOMO coins.
*   **Example**:

    ```python
    libs.Tomochain.setKeys("your_private_key")
    libs.Tomochain.sendTomochain(5, "recipient_wallet_address")
    ```

**libs.web3lib**

Enables Ethereum-compatible blockchain transactions.

* **Functions**:
  * `setKeys(private_key)`: Sets your private key.
  * `sendETHER(value, to, contract, rpc_url, gasPrice=None, private_key=None)`: Sends Ether or tokens.
  * `sendPolygon(value, to, private_key=None)`: Sends MATIC tokens.
*   **Example**:

    ```python
    libs.web3lib.setKeys("your_private_key")
    libs.web3lib.sendETHER(1, "recipient_wallet_address", "contract_address", "https://rpc_url")
    ```

#### **5.5 Summary of Libraries**

| **Library**          | **Purpose**                                 | **Key Functions**                                                   |
| -------------------- | ------------------------------------------- | ------------------------------------------------------------------- |
| **libs.ARB**         | Arbitrum blockchain transactions            | `setKeys`, `send`, `sendARB`                                        |
| **libs.CSV**         | Data management with CSV files              | `create_csv`, `add_row`, `edit_row`, `get`, `delete`                |
| **libs.Coinbase**    | Coinbase payment integration                | `setKeys`, `post`                                                   |
| **libs.Crypto**      | Cryptocurrency conversions and pricing      | `convert`, `get_price`                                              |
| **libs.Random**      | Generating random numbers and strings       | `randomInt`, `randomStr`, `randomFloat`, `randomAscii`              |
| **libs.Polygon**     | Polygon blockchain transactions             | `setKeys`, `send`, `sendPolygon`                                    |
| **libs.Resources**   | Managing user and global resources          | `value`, `add`, `cut`, `set`, `reset`, `getAllData`                 |
| **libs.Paytm**       | Paytm payment integration                   | `setKeys`, `send`                                                   |
| **libs.TTcoin**      | TTcoin cryptocurrency transactions          | `setKeys`, `sendTT`                                                 |
| **libs.Tomochain**   | Tomochain network transactions              | `setKeys`, `sendTRC20`, `sendTomochain`                             |
| **libs.Webhook**     | Creating and managing webhooks              | `genRandomId`, `getUrlFor`                                          |
| **libs.DateAndTime** | Working with dates and times                | `utcnow`, `date_now`, `time`, `now`                                 |
| **libs.Oxapay**      | Oxapay payment integration                  | `post`                                                              |
| **libs.customHTTP**  | Performing HTTP requests with constraints   | `get`, `post`, `put`, `delete`, `head`, `options`, `patch`, `close` |
| **libs.web3lib**     | Ethereum-compatible blockchain transactions | `setKeys`, `sendETHER`, `sendPolygon`                               |

#### **5.6 Real-World Applications of Libraries**

Here are some practical examples of how you can use these libraries in your bots:

**Crypto Payment Bots**

Handle cryptocurrency payments for services or rewards.

*   **Example**:

    ```python
    libs.Polygon.setKeys("your_private_key")
    libs.Polygon.sendPolygon(2.0, "recipient_wallet_address")
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

***

By using these libraries, you can significantly extend the capabilities of your Telegram bots, making them more interactive, efficient, and feature-rich. Whether you need to handle payments, manage data, or integrate with blockchain technologies, TBC’s libraries provide the tools you need to build powerful bots with ease.
