# Coinbase Library TBC

#### **12. Coinbase Library (libs.Coinbase)**

The `libs.Coinbase` library in Telebot Creator integrates seamlessly with the Coinbase API, enabling bots to handle cryptocurrency payments, create addresses, manage transactions, and automate deposit notifications through webhooks.

***

### **12.1 Overview**

The Coinbase integration allows you to:

1. Generate cryptocurrency deposit addresses.
2. Process payments and transactions.
3. Use webhooks to receive real-time updates for deposits.
4. Automate payment responses in your bot.

***

### **12.2 Setting Up Coinbase**

#### **Step 1: Create API Keys in Coinbase**

1. Log in to your Coinbase Commerce account.
2. Go to **Settings > API Keys**.
3. Click **Create an API Key**.
4. Copy the generated API key and save it securely. You’ll need it to configure `libs.Coinbase` in your bot.

***

#### **Step 2: Configure the Coinbase Library**

Set the API keys in your bot using the `libs.Coinbase.setKeys` function:

```python
libs.Coinbase.setKeys("YOUR_API_KEY", "YOUR_API_SECRET")
```

Create a client instance for making API calls:

```python
client = libs.Coinbase.post()
```

***

### **12.3 Creating Cryptocurrency Deposit Addresses**

Generate a unique cryptocurrency address for a user:

```python
address = client.createAddress("BTC")
bot.sendMessage(f"Your Bitcoin deposit address: {address['address']}")
```

**Example: Generating an Ethereum Address**

```python
eth_address = client.createAddress("ETH")
bot.sendMessage(f"Your Ethereum deposit address: {eth_address['address']}")
```

***

### **12.4 Creating a Webhook URL for Deposit Notifications**

#### **Step 1: Generate a Webhook URL**

Use `libs.Webhook.getUrlFor` to create a webhook URL for the `/get_coinbase_updates` command:

```python
webhook_url = libs.Webhook.getUrlFor(
    command="/get_coinbase_updates",
    user_id=12345
)
bot.sendMessage(f"Webhook URL: {webhook_url}")
```

#### **Step 2: Set IPN URL in Coinbase**

1. Log in to Coinbase Commerce.
2. Navigate to **Settings > Notifications**.
3. Paste the webhook URL you generated into the **Webhook URL** field.
4. Click **Save**.

***

### **12.5 Handling Deposit Notifications**

In the `/get_coinbase_updates` command, handle incoming webhook notifications:

```python
# Example command logic for handling Coinbase IPN responses
status = options.json.get("event", {}).get("type", "")
if status == "charge:confirmed":
    bot.sendMessage("Deposit confirmed!")
elif status == "charge:pending":
    bot.sendMessage("Deposit is pending. Waiting for confirmation.")
else:
    bot.sendMessage("Deposit failed or was canceled.")
```

***

### **12.6 Performing Transactions**

#### **Send Cryptocurrency**

Send cryptocurrency to a specific address using `libs.Coinbase`:

```python
transaction = client.sendTransaction({
    "to": "recipient_wallet_address",
    "currency": "BTC",
    "amount": "0.01"
})
bot.sendMessage(f"Transaction initiated! Transaction ID: {transaction['id']}")
```

***

### **12.7 Basic Coinbase Functions**

#### **1. Fetch Payment Status**

Retrieve the status of a payment:

```python
charge_id = "YOUR_CHARGE_ID"
status = client.retrieveCharge(charge_id)["status"]
bot.sendMessage(f"Payment status: {status}")
```

***

#### **2. Fetch Account Balances**

Retrieve your Coinbase wallet balances:

```python
balances = client.getBalance()
bot.sendMessage(f"Your balances: {balances}")
```

***

#### **3. Create a Charge**

Request a payment from a user:

```python
payment = client.createCharge({
    "name": "Subscription Payment",
    "description": "Monthly subscription fee",
    "local_price": {"amount": "10.00", "currency": "USD"},
    "pricing_type": "fixed_price"
})
bot.sendMessage(f"Please complete your payment here: {payment['hosted_url']}")
```

***

#### **4. Verify API Keys**

Check if your API keys are valid:

```python
try:
    client = libs.Coinbase.post()
    bot.sendMessage("API keys are valid!")
except Exception as e:
    bot.sendMessage(f"Invalid API keys: {e}")
```

***

#### **5. Handling Refunds**

Issue refunds directly from the bot:

```python
refund = client.refundCharge("CHARGE_ID")
bot.sendMessage(f"Refund issued: {refund}")
```

***

### **12.8 Summary**

The `libs.Coinbase` library provides all the tools you need to manage cryptocurrency transactions, automate deposit notifications, and handle payments efficiently. By combining this library with webhooks and commands, you can create powerful bots that support seamless crypto integration.
