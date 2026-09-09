# Monetization — Rewarded Ads (libs.tbcads)

*Telebot Creator Documentation — Platform v7.1.2 · Telegram Bot API 10.1*

Earn money from your bot. A user taps **Watch Ad**, views a sponsored offer,
and your bot delivers the reward automatically.

Telebot Creator handles the ad network for you. You need no ad-network
account, no zones, no postback URLs and no external setup. Switch it on, add
**one line** of TPY, and you are live.

---

## 1. Turn it on (no code)

**Creator panel → Monetization → Bots → switch the bot ON** (or *Enable all*).

* the bot needs **500 or more users** (all-time); smaller bots show as *not eligible* until they grow
* ads run only on the bots you switch on
* nothing changes for any other bot

## 2. Show an ad — one line

```python
libs.tbcads.reward_ad("Watch a short ad to unlock your reward", then=".myreward")
```

That is the whole integration. TBC sends the message. The user taps it and goes
to the ad. **A few seconds later, once the ad network confirms the view actually
counted**, TBC finishes the job by itself:

* your **`then` command runs**, where your own reward code lives
* the **button is removed**, so it cannot be used again
* the message becomes your `after_text`, if you set one

Your command carries on normally. `handleNextCommand`, waits, and every other
flow keep working exactly as before.

⚠️ **The reward waits for confirmation, it is not instant.** The ad network
verifies every view and does not count all of them (a repeat view from the same
person, a view it judges invalid, a page that showed no real ad). A view that is
not counted earns nothing, so it rewards nothing. The button stays in place in
that case, so the user can simply tap again.

A fuller example:

```python
shown = libs.tbcads.reward_ad(
    "<b>🎁 Your reward is locked</b>\n\nTap below to view a quick sponsored offer.",
    button="📺 Click here",
    after="after_text",
    after_text="<b>✅ Ad watched!</b>",
    then=".myreward")

if not shown:
    # No ad available right now — never leave the user stuck.
    bot.sendMessage("Ads are not available right now. Please try again shortly.")
```

```python
# .myreward — your reward code, whatever it is
if not libs.tbcads.claim():
    raise ReturnCommand

points = libs.Resources.userRes("points", u)
points.add(10)
bot.sendMessage("✅ +10 points — you now have " + str(points.value()))
```

## 3. Options

```python
libs.tbcads.reward_ad(text=None, button=None, after="delete", after_text=None,
                      then=None, chat_id=None, user=None,
                      parse_mode="HTML", ttl=None)
```

| Option | What it does |
|---|---|
| `text` | the message shown with the button |
| `button` | the button label (default "📺 Watch Ad") |
| `after` | what happens once the view is confirmed — see below |
| `after_text` | replaces the message when `after="after_text"` |
| `then` | the command that runs once the view is confirmed — your reward code |
| `chat_id` | defaults to the user — set it for groups |
| `ttl` | seconds the ad link stays valid (default 900) |

You never pass `bot_id` or `u`. They are taken from the running command.

**`after` modes**

| Mode | Result |
|---|---|
| `"delete"` *(default)* | button removed, text stays |
| `"after_text"` | message replaced by `after_text`, button removed |
| `"keep"` | message left alone (the ad still pays only once) |

All three happen **when the view is confirmed**, not when the button is tapped.
Until then the message is untouched, so a user whose view did not count can tap
again.

**Returns** `True` if the ad was shown, `False` if none was available. Always
handle `False` by giving the reward yourself.

## 4. The `then` command

`then` is where your reward lives. It runs for that user **once the ad network
confirms the view counted** — usually a few seconds after they tap. Points, a
file, unlocking the next step, calling your own API, anything.

If a view is not counted, `then` never runs. That is deliberate: an uncounted
view earns nothing, so paying a reward for it would cost you money for free.

```python
libs.tbcads.reward_ad("Watch to unlock the file", then=".sendfile")
```

```python
# .sendfile
if not libs.tbcads.claim():
    raise ReturnCommand          # not a verified ad view — stop here
bot.sendDocument(FILE_ID, caption="Here is your file 📄")
```

⚠️ **Always start a `then` command with `claim()`.** In TBC, any message whose
text equals a command name runs that command — so without the check a user
could simply type `.sendfile` and get the reward without watching anything.

`claim()` takes **no arguments** inside a `then` command; it reads the bot and
user from the running command. It returns `True` **exactly once per confirmed
ad view**, so a typed name, a replayed link, an unconfirmed view and an expired
one all get nothing. Put your reward *after* the check, never before it.

Inside a `then` command: `u` and `chat_id` are the viewer, `bot_id` is your
bot, and `message` and `params` are `None`. If your `@` command reads
`message.text`, guard it with `if message:`.

## 5. Examples

**Daily bonus**

```python
# /bonus
today = time.strftime("%Y-%m-%d")
if User.getData("bonus_day") == today:
    bot.sendMessage("You already took today's bonus. Come back tomorrow!")
    raise ReturnCommand

User.saveData("bonus_day", today)

if not libs.tbcads.reward_ad("Watch a short ad to claim today's bonus.",
                             button="🎁 Claim bonus",
                             after="after_text",
                             after_text="🎁 Bonus unlocked!",
                             then=".bonus_paid"):
    libs.Resources.userRes("points", u).add(20)
    bot.sendMessage("🎁 +20 bonus points")
```

```python
# .bonus_paid
if not libs.tbcads.claim():
    raise ReturnCommand
libs.Resources.userRes("points", u).add(20)
bot.sendMessage("🎁 +20 points. See you tomorrow!")
```

**Unlock a download**

```python
# /getfile
if not libs.tbcads.reward_ad("The file is ready. Unlock it below.",
                             button="🔓 Unlock download",
                             then=".sendfile"):
    bot.sendDocument(FILE_ID)
```

**In the middle of a flow** — the ad does not interrupt anything:

```python
bot.sendMessage("Step 2 of 3 complete!")
libs.tbcads.reward_ad("Watch an ad for a bonus", then=".bonus5")
bot.sendMessage("Now send me your email:")
handleNextCommand("save_email")
```

## 6. Full manual control

`reward_ad()` covers almost everything. If you want to build the message
yourself — a custom keyboard, an ad inside an existing menu — use the lower
level API.

```python
libs.tbcads.fetch_ad(bot_id, user_id, callback=None, ttl=None)
```
Returns `{"url": ..., "token": ...}` or `None`. Put `url` in a **url** button
(not `web_app`). The `callback` command runs when the link is opened, and must
start with `claim()`.

```python
libs.tbcads.claim()
```
`True` once per **confirmed** ad view, oldest first. A tap that the ad network
did not count never pays. Arguments are optional — `claim(bot_id, user_id)`
also works if you need another user.

```python
libs.tbcads.check(token)
```
The state of one ad session:

| State | Meaning |
|---|---|
| `"pending"` | the link has not been opened |
| `"clicked"` | opened, waiting for the ad network to confirm the view |
| `"done"` | confirmed — this is when `claim()` pays |
| `"expired"` | older than the 15 minute window |

Useful for an *"I watched it"* button instead of a `then` command.

```python
libs.tbcads.is_enabled(bot_id)
```
`True` when monetization is on for this bot.

```python
ad = libs.tbcads.fetch_ad(bot_id, u, callback=".adreward")
if ad:
    keys = [[InlineKeyboardButton(text="📺 Watch Ad", url=ad["url"])]]
    bot.sendMessage("Watch an ad to continue", reply_markup=InlineKeyboardMarkup(keys))
```

With this route you remove the button yourself. `reward_ad()` does it for you.

## 7. How it works

1. `reward_ad()` mints a **single-use link** for that one user, valid 15 minutes.
2. The user taps it and is forwarded to the sponsored offer. Nothing is paid yet.
3. The ad network verifies the view and, if it counts, calls TBC back — normally
   within a few seconds.
4. **On that confirmation** TBC records the impression against your bot, finishes
   the message (`after`), and runs your `then` command so the reward goes out.

Opening the same link again only shows the ad — the reward is released once, by
the first confirmed view. Every link is bound to one bot and one user, so a
leaked link pays nobody, and a view the network rejects pays nobody.

## 8. Earnings, statistics and payout

**Monetization → Stats** shows impressions, CPM and earnings per day and per
bot, over any date range you pick. Every figure there is *your* earnings —
what you can withdraw, not a headline number you have to do maths on.

* **Impressions appear instantly** when the ad network confirms a view, and are
  reconciled **every hour** against the network's own per-bot figures. A number
  can go up when the hourly sync lands; it never double counts.
* **Earnings and CPM follow within an hour or two** of the impressions. Seeing
  impressions before earnings is normal, not a lost payment.
* **Not every tap becomes an impression.** If no ad is available for that user
  at that moment, the tap earns nothing. Taps ≥ impressions ≥ paid impressions
  is the normal shape.

### What the numbers look like at the start

CPM here means **your earnings per 1,000 impressions**. On this format it
lands in the low tens of cents, so early earnings are fractions of a cent and
the panel shows four decimals for them:

| Impressions | Your CPM | Earned |
|---|---|---|
| 13 | $0.33 | $0.0043 |
| 1,000 | $0.33 | $0.33 |
| 100,000 | $0.33 | $33.00 |

CPM is not fixed. It moves with where your users are, the offers available that
day, and how the network rates your traffic — the same bot can earn a different
CPM week to week.

That is not a bug and not a rounding error — it is simply what a handful of
impressions is worth. Meaningful revenue needs volume, so put the ad where
users actually pass through: a daily bonus, a download gate, a step in a flow
people repeat.

**Monetization → Payout**

| | |
|---|---|
| **Hold** | the last **5 days** of earnings, while the traffic is verified |
| **Approved** | verified earnings you can withdraw |
| **Total balance** | Approved + Hold |
| **Total withdrawals** | everything paid out so far |

The minimum payout is **$5**. Enter where to send it and press *Request
payout*; the full approved balance is sent and appears under *History*.

## 9. Rules

Traffic that is not real — self-clicks, click farms, VPN or automated opening
of links, asking users to tap ads from many accounts — is not paid, and
monetization is switched off for that bot.

Rewarding a user for watching an ad is allowed; that is the whole point.
Faking the view is not.

One ad per reward, a clear button, the reward delivered immediately. Users who
trust your bot come back, and that is what pays.

## 10. Troubleshooting

| Symptom | Cause |
|---|---|
| `reward_ad()` returns `False` | monetization is off for this bot, or it has fewer than 500 users. Check **Monetization → Bots**. |
| Button stays after the ad | you used `after="keep"`. Use `"delete"` or `"after_text"`. |
| Reward never arrives | most often the view was not counted by the ad network — nothing is owed, and the button stays so the user can retry. Otherwise: the `then` command name does not exist (it is case-sensitive), or the reward runs before `claim()`. |
| Reward is a few seconds late | expected. TBC waits for the ad network to confirm the view before paying. |
| `then` command errors on `message.text` | `message` is `None` there — guard with `if message:`. |
| Earnings look like `$0.00` | with few impressions the amount is a fraction of a cent — the panel shows 4 decimals (e.g. `$0.0043`). If the CPM column is non-zero, you *are* earning. |
| Earnings behind impressions | normal for an hour or two; impressions are confirmed before revenue is. |
| A tap produced no impression | no ad was available for that user at that moment. Nothing is wrong — that tap simply earns nothing. |
| `claim()` always `False` | the link was opened more than 15 minutes after it was created, or the command was typed rather than triggered by an ad. |
