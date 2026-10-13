---
description: >-
  Open Telegram Mini Apps (Web Apps) from your bot with web_app buttons and read the data the page sends back to a TPY command.
---

# Mini Apps (Web Apps)

A mini app is a web page that opens inside Telegram from a button of your bot. The page is yours: host it on any `https://` address. Telebot Creator runs the bot side.

## Open the page

From a button under a message:

```python
bot.sendMessage("Open the shop:", reply_markup={
    "inline_keyboard": [[{"text": "Open shop", "web_app": {"url": "https://example.com/shop"}}]]
})
```

From the keyboard:

```python
bot.sendMessage("Open the shop:", reply_markup={
    "keyboard": [[{"text": "Open shop", "web_app": {"url": "https://example.com/shop"}}]],
    "resize_keyboard": True,
})
```

From the menu button next to the message field:

```python
bot.setChatMenuButton(chat_id=u, menu_button={
    "type": "web_app",
    "text": "Shop",
    "web_app": {"url": "https://example.com/shop"},
})
```

`web_app` buttons work in private chats only.

## Get data back from the page

Only a page opened from a **keyboard** button can send data straight to the bot. In the page:

```html
<script src="https://telegram.org/js/telegram-web-app.js"></script>
<script>
  Telegram.WebApp.sendData(JSON.stringify({item: "pro", qty: 1}));
</script>
```

The page closes and the bot receives a message without text, which runs the `*` command:

```python
# Command: *
app = message.get("web_app_data")
if app:
    order = encodejson(app.data)
    bot.sendMessage("Order received: " + str(order["qty"]) + " x " + str(order["item"]))
```

- `app.data` is the text the page sent (at most 4096 bytes) and `app.button_text` is the text of the button that opened it.
- `encodejson(text)` turns JSON text into data. Text that contains an apostrophe (`'`) does not parse, so send simple values.
- If the bot is waiting for an answer from this user (`Bot.handleNextCommand`), that command receives the message instead of `*`.

## Pages opened from an inline button or the menu button

These pages cannot use `sendData`. The page reads the user from `Telegram.WebApp.initDataUnsafe.user` and talks to your own server. Your server can then start a bot command through a webhook link made with `libs.Webhook.getUrlFor` (see [TBC Libraries](tbc-libraries-libs.md)).

## Common mistakes

- The address must start with `https://`.
- Telebot Creator does not host the page. Put it on your own hosting.
- `sendData` does nothing in a page opened from an inline button.
