---
description: >-
  Telegram Bot API 10.5 in Telebot Creator: coloured buttons, ephemeral messages, HTML widgets, bot settings, and callApi for any new Telegram method.
---

# Telegram Bot API 10.5 Update

*Telebot Creator Documentation — Platform v7.1.2 · Telegram Bot API 10.5*

*Released: November 2026*

Telebot Creator's `bot` object now covers **every method of Telegram Bot API 10.5**, with every option Telegram lists for it. Every method works in **camelCase** (`bot.sendWidget`) and **snake\_case** (`bot.send_widget`). Existing bot code keeps working: no method was renamed and no argument changed its place.

---

## What's New

### 🎨 Coloured buttons and emoji icons

A button takes `style`: `"success"` (green), `"danger"` (red) or `"primary"` (blue), and `icon_custom_emoji_id` to show a custom emoji before its text. Both now work on button objects as well as on plain dict buttons ([Bot Features 6.13](bot-features-and-functionalities.md) shows the dict form).

```python
kb = InlineKeyboardMarkup()
kb.add(InlineKeyboardButton("Buy now", callback_data="/buy", style="success"),
       InlineKeyboardButton("Cancel", callback_data="/cancel", style="danger"))
bot.sendMessage("Ready to order?", reply_markup=kb)
```

### 👁️ Ephemeral messages

An ephemeral message is a message in a group that only one user can see. Send it with the option `ephemeral_message_parameters`; the message you get back has an `ephemeral_message_id` for editing or deleting it.

```python
sent = bot.sendMessage("Only you can see this.", chat_id=message.chat.id,
                       ephemeral_message_parameters={"receiver_user_id": int(u)})
bot.editEphemeralMessageText(int(u), sent.ephemeral_message_id, "Still only for you.", chat_id=message.chat.id)
```

In a command started by a button, answer only the user who pressed it:

```python
bot.sendMessage("Your code is 4242.", chat_id=call.message.chat.id,
                ephemeral_message_parameters={"callback_query_id": call.id})
```

| Method | Purpose |
|---|---|
| `editEphemeralMessageText` | Change the text of an ephemeral message. |
| `editEphemeralMessageCaption` | Change its caption. |
| `editEphemeralMessageMedia` | Replace its photo, video or file. |
| `editEphemeralMessageReplyMarkup` | Change only its buttons. |
| `deleteEphemeralMessage` | Delete it. |

All five take `receiver_user_id` and `ephemeral_message_id` first. `chat_id` comes last: leave it out only in a private chat, in a group pass `chat_id=message.chat.id`.

### 🧩 HTML widgets

`sendWidget` sends a message that shows your own HTML (up to 32 KB, and 4 KB once compressed). Telegram currently allows widgets in private chats only, and a widget message is always protected: it cannot be forwarded.

```python
bot.sendWidget("<b>Score</b>: 10", height=120, public_data={"score": 10}, allow_queries=True)
```

`public_data` and `chat_data` take text, or a dict that is sent as JSON (up to 1 KB each). With `allow_queries=True` the widget can ask your bot for data: the command **`/handler_widget_query`** runs, with the query as `message`. Answer it within 15 seconds:

```python
bot.answerWidgetQuery(message.id, data={"score": 11})
```

| Method | Purpose |
|---|---|
| `sendWidget` | Send a message with an HTML widget. `is_shared=True` makes a widget you can send again. |
| `sendSharedWidget` | Send a shared widget again by its `widget_id`. |
| `answerWidgetQuery` | Answer a widget's query (within 15 seconds). |
| `editMessageWidgetData` | Change the data of a widget in a message. |
| `editSharedWidgetData` | Change the public data of a shared widget (at most once a second). |

### ⚙️ The bot's own settings

`setMySettings` changes the bot's settings. Only the options you pass change:

```python
bot.setMySettings(can_join_groups=False, has_topic_sidebar=True)
```

| Option | What it sets |
|---|---|
| `supports_inline_queries`, `inline_query_placeholder`, `needs_inline_query_location` | Inline mode, its placeholder text, and whether it asks for the user's location |
| `inline_feedback_share_denominator` | Receive 1 of every 1, 10, 100 or 1000 chosen inline results (`0` = none) |
| `can_join_groups`, `can_read_all_group_messages` | Whether the bot can be added to groups, and privacy mode |
| `has_topics_enabled`, `allows_users_to_create_topics`, `has_topic_sidebar` | Topics in private chats with the bot |
| `can_connect_to_business`, `can_manage_bots`, `can_communicate_with_bots` | Business accounts, managed bots, talking to other bots |
| `supports_guest_queries`, `supports_join_request_queries` | Guest mode and join-request queries |

`callback_query_mode` cannot be changed on Telebot Creator: a button's command runs from the message the button is on, and the other modes take that message away.

### 🎁 Gifts and share screens

| Method | Purpose |
|---|---|
| `getUniqueGift` | Look up a unique (upgraded) gift by its name. |
| `deleteBusinessAccountGift` | Delete a regular gift owned by a connected business account (no Stars are paid out for it). |
| `getChatShareThemeBackdrops` | List the backdrops available for the screen that shares a link to a user or chat. |

### ➕ New options on existing methods

| Option | Methods |
|---|---|
| `ephemeral_message_parameters` | `sendMessage`, `sendPhoto`, `sendVideo`, `sendAnimation`, `sendAudio`, `sendDocument`, `sendVoice`, `sendVideoNote`, `sendSticker`, `sendLocation`, `sendVenue`, `sendContact`, `sendLivePhoto`, `sendRichMessage` |
| `direct_messages_topic_id`, `suggested_post_parameters` | `sendAudio`, `sendDocument`, `sendAnimation`, `sendVoice`, `sendVideoNote`, `sendSticker`, `sendLocation`, `sendVenue`, `sendContact`, `sendDice`, `sendPaidMedia`, `sendInvoice` (the topic id also on `forwardMessages`, `copyMessages`, `sendMediaGroup`) |
| `can_stop`, `keep_on_stop` | `sendMessageDraft`, `sendRichMessageDraft` |
| `can_manage_direct_messages`, `can_manage_tags`, `can_send_welcome_messages` | `promoteChatMember` |
| `custom_description_parse_mode`, `custom_description_entities` | `verifyUser`, `verifyChat` |
| `business_connection_id`, `subscription_period` | `createInvoiceLink` (Stars subscriptions) |
| `message_thread_id` | `sendPaidMedia` |

### 🛠️ Any method by name: `callApi`

When Telegram adds a method, `bot.callApi` reaches it the same day. Pass every argument yourself, including `chat_id`; the answer comes back as Telegram sent it.

```python
backdrops = bot.callApi("getChatShareThemeBackdrops")
bot.sendMessage("Telegram has %d share-screen backdrops." % len(backdrops.backdrops))
```

### 📨 New special commands

| Command | Runs when |
|---|---|
| `/handler_widget_query` | A widget in a message from your bot asks it for data |
| `/handler_shared_widget_query` | A forwarded shared widget asks your bot for data |
| `/handler_subscription` | A user's paid subscription to your bot changes |
| `/handler_stopped_message_generation` | A user asks your bot to stop generating a reply it is streaming as a draft |
| `/handler_managed_bot` | A bot to be managed by yours is created, or a managed bot's token or owner changes |
| `/handler_guest_message` | A guest message arrives; answer it with `bot.answerGuestQuery(message.guest_query_id, ...)` |

Bots started after 25 November 2026 receive these updates. For a bot that was already running, press **Stop** and then **Start** once in the dashboard.

### 🧱 Objects that keep up with Telegram

Every one of Telegram's objects is now available in bot code, for example `EphemeralMessageParameters`, `Widget` or `UniqueGift`. Objects you get back carry every field Telegram sends, also fields Telegram adds later. Objects you build send every option you set, also options newer than this page.

### 🔧 Fixed

- **Reaction objects.** `ReactionTypeEmoji("👍")` and `ReactionTypeCustomEmoji("<custom emoji id>")` are now sent correctly, and `setMessageReaction` takes a list of them:

```python
bot.setMessageReaction(message.chat.id, message.message_id, reaction=[ReactionTypeEmoji("👍")])
```

- **Lists of objects.** `setMyCommands([BotCommand("start", "Start the bot")])`, `answerInlineQuery` with result objects, and invoice `prices` written as `LabeledPrice(...)` no longer fail.
- **Dicts everywhere.** `reply_parameters` can be a dict in every send method. Plain dicts also work for media groups, poll options, sticker sets, Web App answers and media edits.
- **Text mentions.** Entities of type `text_mention` and poll answers built as objects can be sent again.

---

## Not available in bot code

`setWebhook`, `deleteWebhook`, `getWebhookInfo`, `getUpdates`, `logOut` and `close` are refused on `bot`, in `callApi`, and through `HTTP`. Telebot Creator delivers every update to your commands through its own webhook; these methods would cut your bot off from it.

---

## Notes & Requirements

- **Naming.** Every method works in camelCase (`bot.editEphemeralMessageText(...)`) and snake\_case (`bot.edit_ephemeral_message_text(...)`).
- **Current chat.** For send and edit methods, a left-out `chat_id` means the user who ran the command. In a group or channel pass `chat_id=message.chat.id`.
- **Objects or dicts.** Every object argument takes a Telebot Creator object, a dict, or a JSON string.
- **Media.** As everywhere in TBC, pass a Telegram `file_id`, a public URL, or a structured `dict`.
- **Backward compatible.** Existing bot code is unaffected.

The full argument lists are in the [TPY Language Reference](tpy-language-reference.md) (section 4.4.4) and the [TPY Method Index](tpy-method-index.md).
