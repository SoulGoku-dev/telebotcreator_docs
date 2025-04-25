# TON Library Documentation

## Introduction

The TON Library (TonLib) is a powerful addition to TeleBot Creator that enables seamless integration with The Open Network blockchain. With TonLib, you can create wallets, check balances, send TON, work with jettons (TON's tokens), and integrate TON Connect for user wallet connections.

> **Note:** This is a development version of the TON Library introduced in version 4.9.0. Most bugs have been fixed and the library is stable for production use.

## Getting Started

Unlike other libraries, TonLib doesn't require an import statement. It's globally available in your bot code.

```python
# ❌ Don't use imports
# import TonLib  # This will cause an error

# ✅ Instead, use the library directly
wallet = libs.TonLib.generateWallet()
```

## Core Functions

### Wallet Management

#### generateWallet()

Creates a new TON wallet and returns its address and mnemonic phrase.

```python
result = libs.TonLib.generateWallet()
address = result["address"]
mnemonics = result["mnemonics"]
```

#### setKeys(mnemonics)

Stores a mnemonic phrase for later use.

```python
libs.TonLib.setKeys("word1 word2 ... word24")
```

#### getWalletAddress(mnemonics=None)

Retrieves the wallet address from a mnemonic phrase or from stored keys.

```python
# Using stored keys
address = libs.TonLib.getWalletAddress()

# Or with specific mnemonics
address = libs.TonLib.getWalletAddress("word1 word2 ... word24")
```

### TON Operations

#### getBalance(address, api_key=None, endpoint=None)

Checks the TON balance of an address.

```python
balance = libs.TonLib.getBalance("EQD...")
```

#### sendTON(to_address, amount, comment=None, mnemonics=None, api_key=None, endpoint=None, is_testnet=False)

Sends TON to another address.

```python
result = libs.TonLib.sendTON(
    to_address="EQD...",
    amount=0.1,  # In TON
    comment="Payment for service"
)
```

#### checkTONTransaction(address, api_key=None, endpoint=None, limit=10)

Gets the recent transactions for an address.

```python
transactions = libs.TonLib.checkTONTransaction("EQD...")
for tx in transactions:
    if tx["type"] == "incoming":
        from_address = tx["from"]
        amount = tx["amount"]
        # Process the incoming transaction...
```

### TON Connect Integration

#### create_ton_connect_session(user_id, expiry_seconds=86400)

Creates a TON Connect session for wallet connection.

```python
session = libs.TonLib.create_ton_connect_session(user_id="12345")
connect_url = session["connect_url"]
# Send this URL to the user for connection
```

#### verify_ton_connect_session(session_id)

Checks if a wallet has connected to the session.

```python
status = libs.TonLib.verify_ton_connect_session(session_id)
if status["status"] == "connected":
    wallet_address = status["wallet_address"]
    # User wallet is connected
```

#### request_ton_transaction(to_address, amount, comment=None, callback_url="", return_url=None)

Requests a TON transfer from a connected wallet.

```python
request = libs.TonLib.request_ton_transaction(
    to_address="EQD...",
    amount=1.5,
    comment="Donation",
    callback_url="https://your-app.com/callback"
)
# Send the request["connect_url"] to the user
```

### Jetton Operations

#### get_jetton_metadata(jetton_master_address, api_key=None, endpoint=None)

Retrieves information about a Jetton (token).

```python
metadata = libs.TonLib.get_jetton_metadata("EQD...")
name = metadata["name"]
symbol = metadata["symbol"]
total_supply = metadata["total_supply"]
```

#### get_jetton_balance(owner_address, jetton_master_address, api_key=None, endpoint=None)

Checks the Jetton balance of an address.

```python
balance = libs.TonLib.get_jetton_balance(
    owner_address="EQD...",
    jetton_master_address="EQD..."
)
```

#### request_jetton_transfer(to_address, jetton_master_address, amount, comment=None, callback_url="", return_url=None)

Requests a Jetton transfer from a connected wallet.

```python
request = libs.TonLib.request_jetton_transfer(
    to_address="EQD...",
    jetton_master_address="EQD...",
    amount=10,
    callback_url="https://your-app.com/callback"
)
```

## Examples

### Creating a Simple TON Wallet Bot

```python
def handle_start(message):
    # Generate a new wallet
    wallet = libs.TonLib.generateWallet()
    
    # Store the mnemonics
    libs.TonLib.setKeys(wallet["mnemonics"])
    
    # Send information to the user
    bot.send_message(
        chat_id=message.chat.id,
        text=f"Your new TON wallet address:\n`{wallet['address']}`\n\n"
             f"Seed phrase (keep it safe):\n`{wallet['mnemonics']}`",
        parse_mode="Markdown"
    )

def handle_balance(message):
    # Get wallet address
    address = libs.TonLib.getWalletAddress()
    
    # Get balance
    balance = libs.TonLib.getBalance(address)
    
    # Send balance to user
    bot.send_message(
        chat_id=message.chat.id,
        text=f"Your balance: {balance} TON"
    )
```

### TON Connect Integration Example

```python
# Step 1: Initialize wallet connection
def handle_connect_wallet(message):
    user_id = str(message.from_user.id)
    session = libs.TonLib.create_ton_connect_session(user_id)
    
    # Store session_id for later verification
    db.set_user_data(user_id, "ton_session_id", session["session_id"])
    
    # Send connection link to user
    bot.send_message(
        chat_id=message.chat.id,
        text=f"Connect your TON wallet by opening this link:\n{session['connect_url']}"
    )

# Step 2: Check wallet connection status
def handle_check_connection(message):
    user_id = str(message.from_user.id)
    session_id = db.get_user_data(user_id, "ton_session_id")
    
    if not session_id:
        return bot.send_message(message.chat.id, "You haven't started a connection yet")
    
    status = libs.TonLib.verify_ton_connect_session(session_id)
    
    if status["status"] == "connected":
        bot.send_message(
            chat_id=message.chat.id,
            text=f"Wallet connected: {status['wallet_address']}"
        )
    else:
        bot.send_message(
            chat_id=message.chat.id,
            text="Wallet not connected yet. Try again later."
        )
```

## Best Practices

1. **Security**: Never store sensitive mnemonic phrases in plain text. Consider encrypting them or using a secure key management solution.

2. **Error Handling**: Always wrap TON operations in try/except blocks to handle potential errors gracefully.

3. **Testnet First**: When developing, use the testnet (is_testnet=True) before moving to mainnet.

4. **Rate Limiting**: Be mindful of API request limits when checking balances or transactions frequently.

5. **User Experience**: Provide clear instructions and feedback to users, especially for wallet connection steps.

## Limitations

- Maximum code execution time is 120 seconds
- `time.sleep()` function is limited to 10 seconds maximum

## Further Resources

- [The Open Network Documentation](https://ton.org/docs)
- [TON Connect Documentation](https://docs.ton.org/develop/dapps/ton-connect/overview)
- [TBC Libraries Documentation](tbc-libraries-libs.md) 