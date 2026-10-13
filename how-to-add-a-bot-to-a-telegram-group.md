---
description: >-
  How to add a bot to a Telegram group or channel, make it an admin, let it read messages, and make your own bot answer inside the group.
---

# How to Add a Bot to a Telegram Group or Channel

## Add a bot to a group

1. Open the group and tap its name at the top.
2. Tap **Add Members**.
3. Type the bot's username, for example `@YourBotUsername`, select it and confirm.

You can also start from the bot: open its profile, open the menu and choose **Add to Group or Channel**.

If Telegram refuses, either you are not allowed to add members to that group, or the bot's owner switched groups off with `/setjoingroups` in [BotFather](botfather-and-bot-token.md).

## Make the bot an administrator

A bot needs admin rights to delete messages, remove users or pin messages.

1. Open the group info and tap **Administrators**.
2. Tap **Add Admin** and choose the bot.
3. Leave on only the rights the bot needs, then save.

## Add a bot to a channel

A bot can be in a channel only as an administrator.

1. Open the channel info and tap **Administrators**.
2. Tap **Add Admin** and search for the bot's username.
3. Choose its rights, for example **Post Messages**, and save.

## Why the bot does not see messages in the group

Bots start in **privacy mode**. In a group such a bot receives only:

- commands, for example `/help` or `/help@YourBotUsername`
- replies to its own messages
- service messages, for example a member joining

To let the bot read every message, do one of these:

- make it an administrator of the group, or
- send `/setprivacy` to BotFather, choose the bot, press **Disable**, then remove the bot from the group and add it again.

## Make your Telebot Creator bot answer in the group

On Telebot Creator a command answers the **person** who sent it. In a group that means the answer goes to that person's private chat with the bot, and it fails if the person never started the bot. To answer inside the group, pass the group's chat:

```python
# Command: /rules
bot.sendMessage("Be kind. No spam. No ads.", chat_id=message.chat.id)
```

One command can serve both places:

```python
# Command: /start
if message.chat.type == "private":
    bot.sendMessage("Hi! Add me to your group and send /rules there.")
else:
    bot.sendMessage("Hello, everyone! Send /rules to read the group rules.", chat_id=message.chat.id)
```

In a group Telegram may send the command as `/rules@YourBotUsername`. It runs the same `/rules` command.

## Check that a user joined your channel

A common use: the bot works only for people who joined your channel. Make the bot an administrator of the channel, then ask Telegram about the user:

```python
# Command: /start
member = bot.getChatMember("@YourChannelUsername", u)
if member.status in ("creator", "administrator", "member"):
    bot.sendMessage("Welcome! You can use the bot now.")
else:
    bot.sendMessage("Please join @YourChannelUsername first, then send /start again.")
```

## Approve join requests automatically

If the group or channel uses join requests, a bot that is an administrator with the right to add members can approve them. Create a command named `/handler_chat_join_request`:

```python
# Command: /handler_chat_join_request
bot.approveChatJoinRequest(chat_id=message.chat.id, user_id=message.from_user.id)
```

## Common questions

**Can I add any bot to my group?** Yes, unless its owner disabled groups for it.

**Can a bot add itself to a group?** No. A person has to add it.

**Can a bot read the messages of a channel?** Only in channels where it is an administrator.
