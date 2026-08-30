# Auto-Translation (libs.translate)

Your bot can reply to every user in their own language. 92 languages, no API key,
no cost, nothing to install.

There are two ways to use it: flip the **Auto-Translate** switch and change no
code at all, or call `libs.translate` yourself for full control.

---

## 1. Auto-Translate (no code)

**Your Bot → Settings → Auto-Translate → ON**

Every message the bot sends is delivered in the user's own language, detected
from their Telegram language code. Existing commands keep working exactly as
written — you do not change a single line.

## 2. Choose the language yourself

Let the user pick, and their choice is remembered from then on:

```python
libs.translate.setUser("ru")      # this user always gets Russian
libs.translate.getUser()          # what they picked, or None
libs.translate.clearUser()        # back to automatic detection
```

Or set one language for the whole bot:

```python
libs.translate.setGlobal("ar")    # everyone gets Arabic by default
libs.translate.getGlobal()
libs.translate.clearGlobal()
```

Priority: **the user's own choice → your bot default → their Telegram language.**

## 3. Translate text manually

```python
libs.translate.text("Hello!", "hi")          # -> "नमस्ते!"
libs.translate.auto("Hello!", message)       # -> the sender's language
```

## 4. Keep something out of the translation

```python
bot.sendMessage("Welcome! <notrans>MEW Airdrop Bot</notrans> is live.")
```

Anything inside `<notrans>...</notrans>` is delivered exactly as written — useful
for brand names, ticker symbols and slogans.

You can also do the opposite and translate only part of a message:

```python
bot.sendMessage("MEW <trans>Your reward is ready!</trans>")
```

---

## Safe by design

Wallet addresses, amounts, prices, links, `@usernames`, emojis and formatting are
never translated or modified — they are delivered exactly as your code wrote
them. This is guaranteed, not best-effort: if a translation would alter any of
them, the original text is sent instead.

Button labels that trigger commands are also left alone, so your keyboards keep
working in every language.

---

## Supported languages

```python
libs.translate.supported()        # list of language codes
libs.translate.is_supported("hi") # True
```

92 languages including Russian, Hindi, Arabic, Indonesian, Spanish, Portuguese,
Bengali, Urdu, Persian, Turkish, Vietnamese, Chinese, French, German, Ukrainian
and Japanese.

---

## Example: a language picker

Two commands. `/language` shows the menu, `set_language_choice` saves the answer.

**Command: `/language`**

```python
LANGS = [
    ("🇬🇧 English", "en"), ("🇷🇺 Русский", "ru"),
    ("🇮🇳 हिन्दी", "hi"),   ("🇸🇦 العربية", "ar"),
    ("🇮🇩 Indonesia", "id"), ("🇪🇸 Español", "es"),
    ("🇧🇷 Português", "pt"), ("🇹🇷 Türkçe", "tr"),
]

rows = []
for i in range(0, len(LANGS), 2):
    rows.append([{"text": name} for name, _ in LANGS[i:i + 2]])

current = libs.translate.getUser() or "auto"

bot.sendMessage(
    "🌍 <b>Choose your language</b>\n"
    "Current: <b>{}</b>\n\n"
    "Tap a language below.".format(current),
    parse_mode="HTML",
    reply_markup={"keyboard": rows, "resize_keyboard": True,
                  "one_time_keyboard": True}
)
Bot.handleNextCommand("set_language_choice", cancel_at_command=True)
```

**Command: `set_language_choice`**

```python
LANG_MAP = {
    "🇬🇧 English": "en", "🇷🇺 Русский": "ru",
    "🇮🇳 हिन्दी": "hi",   "🇸🇦 العربية": "ar",
    "🇮🇩 Indonesia": "id", "🇪🇸 Español": "es",
    "🇧🇷 Português": "pt", "🇹🇷 Türkçe": "tr",
}

choice = LANG_MAP.get((message.text or "").strip())

if not choice:
    bot.sendMessage("❌ Please tap one of the buttons.",
                    reply_markup={"remove_keyboard": True})
    raise ReturnCommand

libs.translate.setUser(choice)

bot.sendMessage(
    libs.translate.text("✅ Language saved! All messages will now arrive in "
                        "your language.", choice),
    reply_markup={"remove_keyboard": True}
)
```

Note the confirmation is translated into the language they just picked, so the
first thing they see is already in their own language.

---

## Reference

| Call | Does |
|---|---|
| `libs.translate.setUser(lang)` | Remember this user's language |
| `libs.translate.getUser()` | Their saved choice, or `None` |
| `libs.translate.clearUser()` | Forget it, back to automatic |
| `libs.translate.setGlobal(lang)` | Default language for the whole bot |
| `libs.translate.getGlobal()` | The bot default, or `None` |
| `libs.translate.clearGlobal()` | Remove the bot default |
| `libs.translate.text(s, lang)` | Translate a string |
| `libs.translate.auto(s, message)` | Translate into the sender's language |
| `libs.translate.supported()` | All supported language codes |
| `libs.translate.is_supported(lang)` | Is this code supported |
| `<notrans>…</notrans>` | Never translate this part |
| `<trans>…</trans>` | Translate only this part |

`libs.translate.setLang` is kept as an alias of `setUser`.
