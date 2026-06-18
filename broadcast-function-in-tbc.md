# Broadcast Function In TBC

*Telebot Creator Documentation — Platform v7.1.2 · Telegram Bot API 10.1*

#### **Broadcast Function Documentation (Broadcast V2)**

The `broadcast` function sends messages or runs code/commands for many users at once. Broadcasts run asynchronously in a dedicated background daemon, so your command returns immediately with a `broadcast_id` while delivery continues in the background. You can then watch progress, change speed, pause/resume, stop, or rerun the broadcast at any time.

You can call it as either `Bot.broadcast(...)` or `bot.broadcast(...)` — both resolve to the same method.

***

#### **Function Syntax**

```python
Bot.broadcast(
    code=None,
    command=None,
    function=None,
    mode="single",       # "single" | "multi" | "all"
    bot_ids=None,        # list of bot IDs, required when mode="multi"
    speed=8,             # messages per second per bot, 1–25
    callback_url=None,
    bot_id=None,
    api_key=None,
    warnings=None,
    **kwargs             # arguments for the chosen function (text, photo, caption, ...)
)
```

You must provide **exactly one** of `code`, `command`, or `function`.

***

#### **Parameters**

1. **`function`** (_Optional_): A Telegram send method that runs once per recipient. The arguments come from `**kwargs` (e.g. `text=`, `photo=`, `caption=`). Allowed functions:
   * `send_message`, `send_photo`, `send_video`, `send_animation`
   * `send_audio`, `send_document`, `forward_message`, `send_paid_media`
   * `send_sticker`, `send_video_note`, `send_voice`, `send_location`
   * `send_venue`, `send_contact`, `send_poll`, `send_dice`
   * `send_invoice`, `send_game`, `send_media_group`, `pin_chat_message`
   * The `chat_id` is set automatically for each recipient — don't pass it.
2. **`command`** (_Optional_): The name of an existing command in the bot. Its code is run for every user. The command must exist or you get `command doesn't exist`.
3. **`code`** (_Optional_): Custom TPY code executed for each user. The code runs in a restricted sandbox where only `bot` and `u` (the recipient's user id) are available — no builtins.
4. **`mode`** (_Optional_, default `"single"`): See **Broadcast Modes** below.
5. **`bot_ids`** (_Optional_): List of bot ID strings. **Required when `mode="multi"`** (max 500 bots). All listed bots must be owned by the same account.
6. **`speed`** (_Optional_, default `8`): Messages per second **per bot**, clamped to the range **1–25**. At `25` a single bot sends roughly 90,000 messages/hour.
7. **`callback_url`** (_Optional_): URL called as the broadcast progresses.
8. **`bot_id`** + **`api_key`** (_Optional_): Run the broadcast for a different bot you own. If `bot_id` is given, `api_key` is required and must match that bot owner's API key, otherwise you get `API key not valid`.
9. **`warnings`** (_Optional_): Pass `warnings=False` to suppress the test-run notice messages sent to you.

**Return value** (on success):

```python
{"status": "success", "message": "Task added Successfully",
 "broadcast_id": "<id>", "mode": "single", "speed": 8}
```

Keep the `broadcast_id` — every lifecycle method needs it.

***

#### **Broadcast Modes**

| Mode | Who receives it | Requirements |
| --- | --- | --- |
| `"single"` (default) | Users of the current bot only | — |
| `"multi"` | Users of every bot listed in `bot_ids` | `bot_ids` (list, max 500), all owned by you |
| `"all"` | Users of **all** bots owned by the same email | Each bot needs a minimum number of members to be included |

***

#### **Test Run**

Before the broadcast is queued, TBC performs a **test run against you** (the user who triggered the broadcast) so you can confirm the message/code looks right. If the test fails (other than a "chat not found" case, which is skipped), the broadcast is not queued and an error is returned. Pass `warnings=False` to silence the accompanying notice messages.

***

#### **Per-Recipient Placeholders**

Function-mode broadcasts send the **same** `kwargs` to everyone. If you wrote an f-string like `f"Hello {first_name}"`, it would be evaluated once in *your* context and everyone would get *your* name. To greet each user personally, put a **literal placeholder token** (a plain string, **not** an f-string) inside `text` or `caption`. At send time each token is replaced with that recipient's stored profile data:

| Placeholder | Replaced with |
| --- | --- |
| `{user_name}` | The recipient's display name (falls back to username, then `there`) |
| `{first_name}` | Same as `{user_name}` |
| `{username}` | The recipient's `@username` (empty if none) |
| `{user_id}` | The recipient's Telegram user id |
| `{chat_id}` | Same as `{user_id}` |

Substitution is a plain string replace, so stray braces or JSON elsewhere in the text are left untouched, and unknown `{tokens}` pass through verbatim.

```python
# CORRECT — literal placeholder, replaced per recipient
Bot.broadcast(
    function="send_message",
    text="Hello {first_name}! Your ID is {user_id}."
)

# WRONG — f-string is evaluated once in your context
Bot.broadcast(
    function="send_message",
    text=f"Hello {first_name}!"   # everyone receives YOUR name
)
```

***

#### **Validation and Limits**

* **Per-bot limit**: 3 running broadcasts per bot. Exceeding it returns `{"status": "error", "message": "You already have 3 running broadcasts."}`.
* **Global limit**: 5000 running broadcasts across the whole platform. At capacity you get `{"status": "error", "message": "Server currently at max capacity (5000). Please try again later."}`.
* **Function restriction**: only the functions listed above are allowed, otherwise `Function '<name>' not allowed for broadcasting.`
* **Command existence**: a `command` must exist in the bot.
* **Code safety**: `code` broadcasts pass a safety check before they are queued.

***

#### **Usage Examples**

**1. Broadcast a text message**

```python
Bot.broadcast(
    function="send_message",
    text="Hello, everyone! Check out our new feature!"
)
```

**2. Broadcast a photo with a personalized caption**

```python
Bot.broadcast(
    function="send_photo",
    photo="https://example.com/banner.jpg",
    caption="Hi {first_name}, this one's for you!"
)
```

**3. Re-run an existing command for every user**

```python
Bot.broadcast(command="promo_offer")
```

**4. Broadcast with custom code**

```python
Bot.broadcast(
    code="""
bot.sendMessage("Don't miss our latest update!", chat_id=u)
"""
)
```

**5. Broadcast across several of your bots, faster**

```python
Bot.broadcast(
    function="send_message",
    text="Hello {first_name}!",
    mode="multi",
    bot_ids=["123456", "654321"],
    speed=20
)
```

**6. Broadcast to every bot you own**

```python
Bot.broadcast(function="send_message", text="Platform-wide announcement!", mode="all")
```

**7. Broadcast for another bot you own**

```python
Bot.broadcast(
    bot_id="another_bot_id",
    api_key="your_account_api_key",
    function="send_message",
    text="Greetings from Bot B!"
)
```

***

#### **Broadcast Lifecycle Controls**

All of these take the `broadcast_id` returned by `broadcast(...)`.

**Check status** — counts and overall state:

```python
status = Bot.getBroadcastStatus(broadcast_id)
# -> total_users, total_success, total_errors, total_bots, done_bots, speed, status, ...
```

**Detailed progress** — percentage, remaining, and ETA:

```python
progress = Bot.getBroadcastProgress(broadcast_id)
# -> processed, remaining, progress_pct, eta_seconds, speed, status, ...
```

**Change speed on the fly** (1–25, takes effect immediately):

```python
Bot.setBroadcastSpeed(broadcast_id, 15)
```

**Pause and resume** — pausing keeps the broadcast alive; resume restores the prior speed unless you pass a new one:

```python
Bot.pauseBroadcast(broadcast_id)
Bot.resumeBroadcast(broadcast_id)          # restore previous speed
Bot.resumeBroadcast(broadcast_id, speed=5) # or resume at a new speed
```

**Stop** — permanently halts a running broadcast:

```python
Bot.stopBroadcast(broadcast_id)
```

**List broadcasts** for the current bot (optionally filter by status, limit 1–100):

```python
Bot.listBroadcasts()                 # most recent first
Bot.listBroadcasts(status="running")
```

**Rerun** a completed/stopped/failed broadcast with the same parameters (a new `broadcast_id` is generated; you may override the speed):

```python
Bot.rerunBroadcast(broadcast_id)
Bot.rerunBroadcast(broadcast_id, speed=10)
```

**Clear** broadcast records — pass a `broadcast_id` to clear one, or call with no argument to clear all for the bot:

```python
Bot.clearBroadcast(broadcast_id)
Bot.clearBroadcast()   # clears all broadcast records for this bot
```

***

#### **Managing Broadcasts from the Dashboard**

Each bot's **Manage** area includes an admin Broadcast panel (backed by `/v2/bots/{botid}/admin/broadcast/*` endpoints). From there you can create function- or code-mode broadcasts, pick a speed (default 8, max 25), and stop, pause, resume, change speed, or clear running broadcasts — the same controls described above, without writing TPY.

***

#### **Error Handling**

| Error | Cause / Fix |
| --- | --- |
| `You already have 3 running broadcasts.` | Wait for one to finish or stop it. |
| `Server currently at max capacity (5000). Please try again later.` | Global broadcast limit reached; retry later. |
| `command doesn't exist` | The named `command` isn't defined in the bot. |
| `Function '<name>' not allowed for broadcasting.` | Use an allowed function from the list above. |
| `API key not valid` | Check the `bot_id` / `api_key` pair. |
| `Either 'code' or 'function' must be provided.` | Supply one of `code`, `command`, or `function`. |

***

#### **Best Practices**

1. Use **function mode** for plain messages/media (fastest) and **code mode** only when you need per-user logic.
2. Personalize with literal placeholders (`{first_name}`, `{user_id}`, …) — never f-strings.
3. Start at a modest `speed` and raise it with `setBroadcastSpeed` while watching `getBroadcastProgress`.
4. Never expose `api_key` in code or logs.
5. Clear finished broadcasts with `clearBroadcast` to keep your status list tidy.
