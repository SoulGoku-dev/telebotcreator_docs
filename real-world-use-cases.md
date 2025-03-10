# Real-World Use Cases

#### **8. Real-World Use Cases**

This section demonstrates how to apply Telebot Creator’s features and libraries in real-world scenarios. By combining workflows, advanced commands, and external integrations, you can create bots that solve practical problems and enhance user engagement.

***

### **8.1 Referral System**

#### **Overview**

A referral system tracks users who invite others to the bot and rewards them with points or other incentives. This use case involves:

1. Generating unique referral links.
2. Tracking referrals.
3. Rewarding users based on their referral count.
4. Displaying leaderboards for top referrers.

***

#### **Implementation**

**Step 1: Generate Unique Referral Links**

In the `/start` command, include the user’s ID as a parameter to generate a referral link:

```python
bot.sendMessage(f"Invite your friends using this link: t.me/{bot.username}?start={u}")
```

**Step 2: Track Referrals**

In the `/start` command, check if a referral ID is provided:

```python
referrer_id = params
if referrer_id:
    referrer_points = libs.Resources.userRes("points", referrer_id)
    referrer_points.add(10)
    bot.sendMessage(f"User {referrer_id} has earned 10 points for referring you!")
```

**Step 3: Reward Users**

Track and display referral rewards dynamically:

```python
user_points = libs.Resources.userRes("points", u)
bot.sendMessage(f"You have {user_points.value()} points!")
```

**Step 4: Create a Leaderboard**

Display the top referrers using `libs.Resources`:

```python
top_referrers = libs.Resources.userRes("points").getAllData(5)
leaderboard = "\n".join([f"{i+1}. User {entry['user']}: {entry['value']} points" for i, entry in enumerate(top_referrers)])
bot.sendMessage(f"Top Referrers:\n{leaderboard}")
```

***

### **8.2 Payment Automation Bot**

#### **Overview**

This bot automates payment handling using the `libs.Coinbase` library. It can:

1. Generate payment requests.
2. Confirm payment status.
3. Notify users of successful payments.

***

#### **Implementation**

**Step 1: Set Up Coinbase Client**

Configure the Coinbase client with your API keys:

```python
libs.Coinbase.setKeys("your_api_key", "your_api_secret")
client = libs.Coinbase.post()
```

**Step 2: Generate Payment Requests**

Request payment for specific amounts:

```python
payment_details = client.createCharge({
    "name": "Subscription Payment",
    "description": "Monthly subscription fee",
    "local_price": {"amount": "10.00", "currency": "USD"},
    "pricing_type": "fixed_price"
})
bot.sendMessage(f"Please make your payment here: {payment_details['hosted_url']}")
```

**Step 3: Verify Payment Status**

Check payment status using the charge ID:

```python
charge_id = "charge_id_from_payment"
status = client.retrieveCharge(charge_id)['status']
if status == "CONFIRMED":
    bot.sendMessage("Payment confirmed! Thank you!")
else:
    bot.sendMessage(f"Payment status: {status}")
```

***

### **8.3 Survey and Data Collection Bot**

#### **Overview**

This bot collects user input for surveys or forms and stores the data in a CSV file for easy analysis.

***

#### **Implementation**

**Step 1: Collect User Responses**

Ask users a series of questions:

```python
bot.sendMessage("What is your name?")
Bot.handleNextCommand("get_name")
```

Store the responses:

```python
name = msg
User.saveData("name", name)
bot.sendMessage("What is your email?")
Bot.handleNextCommand("get_email")
```

**Step 2: Save Data to CSV**

Save the collected data into a CSV file using `libs.CSV`:

```python
csv_handler = libs.CSV.CSVHandler("survey_data.csv")
csv_handler.create_csv(["Name", "Email"])
csv_handler.add_row({"Name": User.getData("name"), "Email": User.getData("email")})
bot.sendMessage("Your responses have been saved. Thank you!")
```

***

### **8.4 Crypto Airdrop Bot**

#### **Overview**

This bot automates cryptocurrency distributions using the `libs.Polygon` library.

***

#### **Implementation**

**Step 1: Configure Polygon Keys**

Set the private key for transactions:

```python
libs.Polygon.setKeys("your_private_key")
```

**Step 2: Automate Token Transfers**

Send tokens to multiple recipients:

```python
recipients = [
    {"address": "0xRecipient1", "amount": 10},
    {"address": "0xRecipient2", "amount": 15}
]

for recipient in recipients:
    libs.Polygon.send(
        value=recipient["amount"],
        to=recipient["address"],
        contract="contract_address"
    )
    bot.sendMessage(f"Sent {recipient['amount']} tokens to {recipient['address']}")
```

***

### **8.5 Real-Time Notification Bot**

#### **Overview**

This bot uses `libs.Webhook` to send real-time updates based on external events, such as sales or user actions.

***

#### **Implementation**

**Step 1: Generate Webhook URL**

Generate a webhook URL for notifications:

```python
webhook_url = libs.Webhook.getUrlFor("send_notification", user_id=12345)
bot.sendMessage(f"Webhook URL: {webhook_url}")
```

**Step 2: Process Webhook Events**

Handle incoming webhook events in a command:

```python
bot.sendMessage("You have a new sale! Congratulations!")
```

***

### **8.6 Event Management Bot**

#### **Overview**

This bot manages events, allowing users to RSVP, receive reminders, and track attendance.

***

#### **Implementation**

**Step 1: RSVP System**

Allow users to RSVP to an event:

```python
bot.sendMessage("Would you like to attend the event? Reply with 'Yes' or 'No'.")
Bot.handleNextCommand("process_rsvp")
```

Store responses:

```python
response = msg
if response.lower() == "yes":
    User.saveData("RSVP", "Yes")
    bot.sendMessage("Thank you for RSVPing!")
else:
    bot.sendMessage("Maybe next time!")
```

**Step 2: Event Reminders**

Send reminders using `runCommandAfter`:

```python
Bot.runCommandAfter(3600, "send_event_reminder")
```

In the reminder command:

```python
bot.sendMessage("Reminder: The event starts in 1 hour!")
```

***

### **8.7 Tips and Best Practices**

1. **Optimize Point Usage**:
   * Combine commands where possible.
   * Use wildcards (`*`) for unstructured messages to reduce redundant commands.
2. **Handle Large User Bases**:
   * Use in-built broadcasting strategies.
   * Target active users only.
3. **Secure Data**:
   * Encrypt sensitive user data.
   * Use HTTPS webhooks for secure communication.
