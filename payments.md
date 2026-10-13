# Payments (Telegram Stars and Invoices)

A bot can sell digital goods for Telegram Stars, or other goods through a payment provider connected in @BotFather. A payment has three steps, each in its own command.

## 1. Send the invoice

```python
# Command: /buy
bot.sendInvoice(
    title="Pro plan",
    description="30 days of Pro",
    invoice_payload="pro_30",
    provider_token="",
    currency="XTR",
    prices=[{"label": "Pro plan", "amount": 50}],
    chat_id=u,
)
```

- `chat_id=u` is required. `sendInvoice` does not fill in the current chat by itself.
- For Stars use `currency="XTR"` and an empty `provider_token`. `amount` is the number of Stars.
- For money through a provider, put the provider token from @BotFather in `provider_token`, the currency code (`"USD"`, `"EUR"`) in `currency`, and give `amount` in the smallest unit: `500` is 5.00.
- `invoice_payload` is your own label for the purchase. The user never sees it, and you get it back when the payment arrives.

To get a payment link instead of a message:

```python
link = bot.createInvoiceLink("Pro plan", "30 days of Pro", "pro_30", "", "XTR", [{"label": "Pro plan", "amount": 50}])
bot.sendMessage("Pay here: " + link)
```

## 2. Approve the checkout

When the user presses Pay, Telegram asks the bot to confirm and waits 10 seconds for the answer. Create a command named `/handler_pre_checkout_query`:

```python
# Command: /handler_pre_checkout_query
bot.answerPreCheckoutQuery(message.id, True)
```

Without this command every payment fails. To refuse a payment:

```python
bot.answerPreCheckoutQuery(message.id, False, error_message="Sold out")
```

In this command `message` is the checkout request: `message.id`, `message.from_user.id`, `message.currency`, `message.total_amount` and `message.invoice_payload`.

## 3. Deliver after the payment

The confirmation is a message without text, so it runs the `*` command. Put this at the top of `*`:

```python
# Command: *
pay = message.get("successful_payment")
if pay:
    charge = pay.telegram_payment_charge_id
    if not User.getData("paid_" + charge):
        User.saveData("paid_" + charge, pay.invoice_payload)
        if pay.invoice_payload == "pro_30":
            User.saveData("pro_until", time.time() + 30 * 86400)
        bot.sendMessage("Payment received. Pro is active for 30 days.")
```

- `pay` holds `invoice_payload`, `total_amount`, `currency` and `telegram_payment_charge_id`.
- Saving the charge id first makes sure one payment is delivered once, even if the update arrives twice.
- If the bot is waiting for an answer from this user (`Bot.handleNextCommand`), that command receives the payment message instead of `*`.

## Refund a Stars payment

```python
bot.refundStarPayment(int(u), charge)
```

`charge` is the `telegram_payment_charge_id` saved in step 3.

## Checklist

1. `/buy` (or a button) sends the invoice with `chat_id=u`.
2. `/handler_pre_checkout_query` answers `True`.
3. `*` checks `message.get("successful_payment")` and delivers.
