---
description: >-
  How to make a ChatGPT Telegram bot: connect the OpenAI API to your bot with a few lines of code, add memory, and host it for free.
---

# How to Make a ChatGPT Telegram Bot

This guide builds a Telegram bot that answers every message with an OpenAI model, the same family of models that powers ChatGPT. The bot is hosted for free on Telebot Creator. The AI answers are billed by OpenAI to your own API key.

## What you need

- A bot on Telebot Creator. The [quick start](getting-started.md) takes about five minutes.
- An OpenAI API key from [platform.openai.com](https://platform.openai.com/api-keys). The API is a paid service of OpenAI, separate from a ChatGPT subscription.

## Step 1: Save your API key

Keep the key in the bot's data, not in the code of your commands. Create this command, send it to your bot once, then delete the command:

```python
# Command: /setkey
Bot.saveData("ai_key", "PASTE_YOUR_OPENAI_KEY_HERE")
bot.sendMessage("Key saved. Now delete the /setkey command.")
```

## Step 2: Answer every message

Create a command named `*`. It runs for any message that is not another command.

```python
# Command: *
key = Bot.getData("ai_key")
if msg == "None":
    bot.sendMessage("Please send me a text message.")
else:
    bot.sendChatAction("typing")
    try:
        answer = libs.openai_lib.chat(
            key,
            msg,
            system="You are a helpful assistant inside a Telegram bot. Keep answers short.",
        )
    except Exception as e:
        answer = "The AI did not answer. Please try again in a minute."
    bot.sendMessage(answer[:4000], parse_mode="")
```

- `libs.openai_lib.chat` sends one question and returns the answer as plain text.
- `system` is the instruction that sets the bot's character. Change it to make a support agent, a tutor or a translator.
- `parse_mode=""` sends the answer as plain text. Without it a `<` or `&` in the answer can break the message.
- `answer[:4000]` keeps the text inside Telegram's limit of 4,096 characters for one message.
- A message without text, such as a photo, arrives as the text `None`.

Start the bot and write to it in Telegram.

## Step 3: Add a welcome message

```python
# Command: /start
bot.sendMessage("Hi! I am an AI assistant. Ask me anything.")
```

## Make the bot remember the conversation

The version above answers each message on its own. To give the bot memory, keep the last messages of each user and send them along. Replace the code of `*` with this:

```python
# Command: *
key = Bot.getData("ai_key")
history = User.getData("history") or []
history.append({"role": "user", "content": msg})
history = history[-10:]

client = libs.openai_lib.OpenAIClient(api_key=key, timeout=120)
result = client.create_chat_completion(
    messages=[{"role": "system", "content": "You are a helpful assistant inside a Telegram bot. Keep answers short."}] + history,
)
answer = result["choices"][0]["message"]["content"]

history.append({"role": "assistant", "content": answer})
User.saveData("history", history[-10:])
bot.sendMessage(answer[:4000], parse_mode="")
```

Let users clear the memory:

```python
# Command: /new
User.saveData("history", [])
bot.sendMessage("Started a new conversation.")
```

## Choose the model

`chat` uses `gpt-4o-mini` unless you name another model: `libs.openai_lib.chat(key, msg, model="MODEL_NAME")`. To see the models your key can use:

```python
# Command: /models
key = Bot.getData("ai_key")
bot.sendMessage("\n".join(libs.openai_lib.models(key)[:40]), parse_mode="")
```

## Use Gemini or OpenRouter instead

The same call works with a Google Gemini key or an OpenRouter key. Save that key with `/setkey` in the same way, then name the provider:

```python
# Command: *
key = Bot.getData("ai_key")
answer = libs.openai_lib.chat(key, msg, provider="gemini")
bot.sendMessage(answer[:4000], parse_mode="")
```

Use `provider="openrouter"` for OpenRouter. If the default model is not available for your key, list the models with `libs.openai_lib.models(key, provider="gemini")` and pass one as `model=`.

## Common questions

**Is the bot free to run?** Hosting on Telebot Creator is free. OpenAI charges your API key for the answers.

**Can other people see my API key?** No. It is stored in your bot's data. Do not put it in a message or in the text of a command you share.

**The bot answers "The AI did not answer".** Check that the key is right and that your OpenAI account has credit. If the model is not offered to your key any more, run `/models` and pass one of the names as `model=`. To see the real error while you test, send `str(e)` instead of the fixed text.
