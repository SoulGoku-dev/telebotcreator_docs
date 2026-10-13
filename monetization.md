---
description: >-
  Earn money from a Telegram bot with rewarded ads: turn ads on, show an ad with libs.tbcads, reward the user, and follow earnings and payouts.
---

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
libs.tbcads.reward_ad("Watch a short ad to unlock your reward", then="/myreward")
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
    then="/myreward")

if not shown:
    # No ad available right now — never leave the user stuck.
    bot.sendMessage("Ads are not available right now. Please try again shortly.")
```

```python
# /myreward — your reward code, whatever it is
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
libs.tbcads.reward_ad("Watch to unlock the file", then="/sendfile")
```

```python
# /sendfile
if not libs.tbcads.claim():
    raise ReturnCommand          # not a verified ad view — stop here
bot.sendDocument(FILE_ID, caption="Here is your file 📄")
```

⚠️ **Always start a `then` command with `claim()`.** In TBC, any message whose
text equals a command name runs that command — so without the check a user
could simply type `/sendfile` and get the reward without watching anything.

`claim()` takes **no arguments** inside a `then` command; it reads the bot and
user from the running command. It returns `True` **exactly once per confirmed
ad view**, so a typed name, a replayed link, an unconfirmed view and an expired
one all get nothing. Put your reward *after* the check, never before it.

Inside a `then` command: `u` and `chat_id` are the viewer, `bot_id` is your
bot, and `message` and `params` are `None`. If your `@` command reads
`message.text`, guard it with `if message:`.

## 5. Put the ad anywhere: `link()`

`reward_ad()` sends its own message. When you want the ad inside **your**
message, in your own keyboard or next to your other buttons, ask for just the
link:

```python
ads_link = libs.tbcads.link()
```

That is the ad URL for the user running the command. Use it like any other URL:

```python
# /menu
ads_link = libs.tbcads.link(then="/myreward")

keyboard = [[{"text": "💰 Balance", "callback_data": "/balance"}]]
if ads_link:
    keyboard.insert(0, [{"text": "📺 Watch Ad (+10 points)", "url": ads_link}])

bot.sendMessage("Choose an option:", reply_markup={"inline_keyboard": keyboard})
```

```python
libs.tbcads.link(then=None, ttl=None)
```

| Option | What it does |
|---|---|
| `then` | optional. The command that runs once the view is confirmed, exactly as in `reward_ad()`. Start it with `claim()` |
| `ttl` | seconds the link stays valid (default 900) |

**Returns** the URL, or `None` if no ad is available. Always check it before
you put it in a button.

Three things to know:

* **Make a new link each time you send the message.** One link counts one ad
  view, belongs to that one user and is valid for 15 minutes. Do not save it or
  reuse it for other users: the reward always goes to the user it was made for.
* **Without `then`, nothing runs afterwards.** The view still counts for your
  earnings; you simply give no reward.
* **TBC does not edit your message.** With `reward_ad()` the button is removed
  for you. Here the message is yours, so the button stays. Once the view is
  confirmed, tapping it again only shows a short "already watched" page.

## 6. Examples

**Daily bonus**

```python
# /bonus
today = libs.DateAndTime.utcnow()["date"]      # "2026-10-12" (time has only time() and sleep())
if User.getData("bonus_day") == today:
    bot.sendMessage("You already took today's bonus. Come back tomorrow!")
    raise ReturnCommand

User.saveData("bonus_day", today)

if not libs.tbcads.reward_ad("Watch a short ad to claim today's bonus.",
                             button="🎁 Claim bonus",
                             after="after_text",
                             after_text="🎁 Bonus unlocked!",
                             then="/bonus_paid"):
    libs.Resources.userRes("points", u).add(20)
    bot.sendMessage("🎁 +20 bonus points")
```

```python
# /bonus_paid
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
                             then="/sendfile"):
    bot.sendDocument(FILE_ID)
```

**In the middle of a flow** — the ad does not interrupt anything:

```python
bot.sendMessage("Step 2 of 3 complete!")
libs.tbcads.reward_ad("Watch an ad for a bonus", then="/bonus5")
bot.sendMessage("Now send me your email:")
handleNextCommand("save_email")
```

## 7. Check if it's on

```python
libs.tbcads.is_enabled(bot_id)
```
`True` when monetization is on for this bot — handy for a small status command.

`reward_ad()` and `link()` are the whole ad API. `reward_ad()` shows the message,
waits for the ad network to confirm the view, runs your `then` command and
removes the button. `link()` hands you the URL and leaves the message to you.
There is nothing lower-level to wire up yourself.

## 8. How it works

1. `reward_ad()` or `link()` mints a **single-use link** for that one user, valid 15 minutes.
2. The user taps it and is forwarded to the sponsored offer. Nothing is paid yet.
3. The ad network verifies the view and, if it counts, calls TBC back — normally
   within a few seconds.
4. **On that confirmation** TBC records the impression against your bot, finishes
   the message (`after`), and runs your `then` command so the reward goes out.

Opening the same link again only shows the ad — the reward is released once, by
the first confirmed view. Every link is bound to one bot and one user, so a
leaked link pays nobody, and a view the network rejects pays nobody.

## 9. Earnings, statistics and payout

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

CPM here means **your earnings per 1,000 impressions**. One impression is worth
a small fraction of that, so with only a few impressions the amount is a
fraction of a cent and the panel shows four decimals for it.

There is no single rate. CPM depends mostly on which countries your users are
in, then on the offers available that day and how the network rates your
traffic. As a guide, per 1,000 impressions:

| Your users are in | CPM guide |
|---|---|
| Tier 1 countries, high-quality traffic | $5 – $10 |
| Tier 2 countries | $2 – $5 |
| Tier 3 countries | $0.50 – $2 |

These are guide ranges, not fixed prices. Real users earn the most; VPN, proxy
or low-quality traffic earns far less, whatever country it appears to come
from. Your own CPM is always shown in **Monetization → Stats**.

A tiny amount early on is not a bug and not a rounding error. It is simply what
a handful of impressions is worth. Meaningful revenue needs volume, so put the
ad where users actually pass through: a daily bonus, a download gate, a step in
a flow people repeat.

**Monetization → Payout**

| | |
|---|---|
| **Hold** | the last **2 days** of earnings, while the traffic is verified |
| **Approved** | verified earnings you can withdraw |
| **Total balance** | Approved + Hold |
| **Total withdrawals** | everything paid out so far |

The minimum payout is **$5**. Enter where to send it and press *Request
payout*; the full approved balance is sent and appears under *History*.

## 10. Getting more impressions

Turning ads on does **not** start the earnings. Enrolling only makes your bot
eligible; nothing is shown until `libs.tbcads.reward_ad(...)` actually runs in
one of your commands. By far the most common reason a bot earns `$0.00` is that
the line was never added anywhere users reach.

Once it is in, volume is the whole game. CPM is set by the network and moves on
its own; the number of impressions is the part you control.

**Put it where people already go.** One ad on a screen everyone passes beats
five on screens nobody opens. Ranked by how well they tend to work:

| Placement | Why it works |
|---|---|
| **Unlocking something they asked for** — a download, a file, a result | They already want it, so almost everyone taps |
| **A daily bonus or streak** | Brings the same user back tomorrow, so one user counts many times |
| **A retry, or skipping a wait** | Fires exactly when the user is motivated |
| **A step inside a flow they repeat** | Rides traffic you already have |
| A standalone "watch an ad" button on a menu | Only the curious ever press it |

Section 6 has working code for the first three.

**Give more than one chance per session.** A user who downloads three files can
see three ads. Put the call in the command that repeats, not in `/start`.

**Returning users are worth more than new ones.** Ten people who come back daily
produce far more impressions in a month than a hundred who visit once. A daily
bonus is the cheapest way to build that habit.

**Always handle the no-ad case.** `reward_ad` returns `False` when no ad was
available for that user at that moment. Give the reward yourself when it does,
or users hit a dead end and stop coming back:

```python
if not libs.tbcads.reward_ad("Watch a short ad to unlock", then="/reward"):
    give_the_reward_anyway()
```

**Say what the button is.** "Watch a short ad to unlock" converts about as well
as anything vague, and users who know what they tapped come back. Labelling an
ad as something else — a captcha, a "verify", a fake download button — is
counted as fraudulent traffic: those impressions are not paid, and it puts the
whole account at risk. See section 11.

**What will not work:** tapping your own ads, asking friends or a group to tap,
multiple accounts, emulators or scripted opening of links. That traffic is
detected, removed from your earnings, and repeated attempts end the account.
Real users on a bot they actually use is the only thing that pays.

## 11. Rules

Traffic that is not real — self-clicks, click farms, VPN or automated opening
of links, asking users to tap ads from many accounts — is not paid, and
monetization is switched off for that bot.

Rewarding a user for watching an ad is allowed; that is the whole point.
Faking the view is not.

One ad per reward, a clear button, the reward delivered immediately. Users who
trust your bot come back, and that is what pays.

## 12. Troubleshooting

| Symptom | Cause |
|---|---|
| `reward_ad()` returns `False`, or `link()` returns `None` | monetization is off for this bot, or it has fewer than 500 users. Check **Monetization → Bots**. |
| Button stays after the ad | you used `after="keep"`. Use `"delete"` or `"after_text"`. |
| Reward never arrives | most often the view was not counted by the ad network — nothing is owed, and the button stays so the user can retry. Otherwise: the `then` command name does not exist (it is case-sensitive), or the reward runs before `claim()`. |
| Reward is a few seconds late | expected. TBC waits for the ad network to confirm the view before paying. |
| `then` command errors on `message.text` | `message` is `None` there — guard with `if message:`. |
| Earnings look like `$0.00` | with few impressions the amount is a fraction of a cent — the panel shows 4 decimals (e.g. `$0.0043`). If the CPM column is non-zero, you *are* earning. |
| Earnings behind impressions | normal for an hour or two; impressions are confirmed before revenue is. |
| A tap produced no impression | no ad was available for that user at that moment. Nothing is wrong — that tap simply earns nothing. |
| `claim()` always `False` | the link was opened more than 15 minutes after it was created, or the command was typed rather than triggered by an ad. |
