# Files and Media

A command cannot read or write files on a disk. A file is always one of three things: a Telegram `file_id`, a public link, or bytes kept in a variable.

## Receiving a file

A photo, document, video, voice message or sticker arrives as a message without text. It runs the `*` command, or the command set with `Bot.handleNextCommand` when the bot is waiting for an answer.

```python
# Command: *
photo = message.get("photo")
doc = message.get("document")
if photo:
    User.saveData("last_photo", photo[-1].file_id)
    bot.sendMessage("Photo saved.")
elif doc:
    User.saveData("last_file", doc.file_id)
    bot.sendMessage("Saved " + str(doc.get("file_name")) + ".")
```

| Field | What it holds |
| --- | --- |
| `message.photo` | the sizes of one photo, smallest first. Use `message.photo[-1].file_id` for the largest |
| `message.document` | `file_id`, `file_name`, `mime_type`, `file_size` |
| `message.video`, `message.audio`, `message.voice`, `message.animation`, `message.sticker` | `file_id` and details of that kind of file |
| `message.caption` | the text written under the file |

A `file_id` is a short text. Save it with `User.saveData` or `Bot.saveData` and you can send the file again at any time. It works only in the bot that received it.

## Sending a file

```python
bot.sendPhoto(User.getData("last_photo"), caption="Your photo")
bot.sendPhoto("https://example.com/banner.jpg")
bot.sendDocument("id,name\n1,Ann\n".encode(), visible_file_name="users.csv", caption="Export")
```

The first line sends by `file_id`, the second by link (Telegram downloads it, so the link must be public), the third sends bytes made inside the command under the name `users.csv`.

The same works for `bot.sendVideo`, `bot.sendAudio`, `bot.sendVoice`, `bot.sendAnimation` and `bot.sendSticker`. The file goes to the user who triggered the command; add `chat_id=...` for another chat.

## Reading what is inside a file

```python
# Command: *
doc = message.get("document")
if doc:
    raw = HTTP.get(bot.getFileUrl(doc.file_id)).content
    text = raw.decode("utf-8")
    bot.sendMessage("The file has " + str(len(text.splitlines())) + " lines.")
```

- `bot.getFileUrl(file_id)` gives a download link and `HTTP.get(link).content` gives the bytes. Telegram lets a bot download files of up to 20 MB.
- The link contains the bot token. Never send it to a user and never save it.
- On a bot with **Faster Response** turned on this download does not work. Pass the file on by its `file_id` instead.

## Common mistakes

- `open("file.txt")` and file paths do not exist in a command. Use a `file_id`, a link or bytes.
- A `file_id` from one bot does not work in another bot.
- A message with a file has no text: `msg` is the text `None`. Check `message.get("photo")` or `message.get("document")`, not `msg`.
