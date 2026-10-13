---
description: >-
  Inline keyboards, reply keyboards and callback buttons for Telegram bots in TPY: how to build them and which command runs when a user taps.
---

# Keyboards and Buttons

A keyboard is passed as `reply_markup` to any sending method. Write it as a plain dictionary.

## Inline buttons (under a message)

```python
bot.sendMessage("Main menu", reply_markup={
    "inline_keyboard": [
        [{"text": "Balance", "callback_data": "/balance"}, {"text": "Buy 5", "callback_data": "/buy 5"}],
        [{"text": "Our channel", "url": "https://t.me/telegram"}],
    ]
})
```

Each inner list is one row. A button needs `text` and one action: `callback_data` (runs a command), `url` (opens a link) or `web_app` (opens a page, see [Mini Apps](mini-apps.md)).

### Which command runs when a button is pressed

| `callback_data` | Command that runs |
| --- | --- |
| `/balance` | the command named `/balance` |
| `/buy 5` | the command `/buy`, with `params` equal to `5` |
| `balance` | the command named exactly `balance` |
| no command with that name | the `*` command, if the bot has one |

`callback_data` holds at most 64 bytes.

### Inside a command started by a button

| Name | What it holds |
| --- | --- |
| `u` | id of the user who pressed the button |
| `call.id` | id of the press, needed by `bot.answerCallbackQuery` |
| `msg` | the whole `callback_data` |
| `params` | the part after the first space when `callback_data` starts with `/` (the text `None` when there is nothing) |
| `message` | the message the button is attached to |

`call` exists only when a button started the command. A command that is also typed by users must not use it.

```python
# Command: /buy      button: {"text": "Buy 5", "callback_data": "/buy 5"}
bot.answerCallbackQuery(call.id, "Added")
bot.editMessageText("You bought " + params + " items.", message_id=message.message_id)
```

`bot.answerCallbackQuery(call.id, "Not enough balance", show_alert=True)` shows the text in a pop-up window. Call `bot.answerCallbackQuery(call.id)` even with no text: it stops the loading circle on the button.

`bot.editMessageText` changes the message in place and can carry a new `reply_markup`. In a group add `chat_id=message.chat.id`.

## Reply keyboard (in place of the phone keyboard)

```python
bot.sendMessage("Choose:", reply_markup={
    "keyboard": [[{"text": "Balance"}, {"text": "Help"}]],
    "resize_keyboard": True,
})
```

Pressing a button sends its text as a normal message. The command whose name is exactly that text runs (here the commands named `Balance` and `Help`). With no such command, `*` runs.

Other options next to `resize_keyboard`: `one_time_keyboard`, `is_persistent`, `input_field_placeholder`.

Remove the keyboard:

```python
bot.sendMessage("Menu closed.", reply_markup={"remove_keyboard": True})
```

### Ask for a phone number or a location

```python
bot.sendMessage("Please share your phone number.", reply_markup={
    "keyboard": [[{"text": "Share my phone", "request_contact": True}]],
    "resize_keyboard": True,
    "one_time_keyboard": True,
})
```

Use `"request_location": True` for a location. The answer is a message without text, so it runs the `*` command (or the command set with `Bot.handleNextCommand`):

```python
# Command: *
contact = message.get("contact")
if contact:
    User.saveData("phone", contact.phone_number)
    bot.sendMessage("Saved.", reply_markup={"remove_keyboard": True})
```

## Good to know

- The classes `InlineKeyboardMarkup`, `InlineKeyboardButton`, `ReplyKeyboardMarkup`, `KeyboardButton` and `ReplyKeyboardRemove` also work. A dictionary is shorter and does the same.
- Button colours and custom emoji are described in [Bot Features and Functionalities](bot-features-and-functionalities.md).
- A keyboard is sent to the user who triggered the command. Add `chat_id=...` to send it to another chat.
