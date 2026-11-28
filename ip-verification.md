---
description: >-
  IP verification for Telegram bots: allow only a set number of accounts per internet connection and block VPNs, checked in a Telegram Mini App. One line with libs.IPVerify.
---

# IP Verification (libs.IPVerify)

*Telebot Creator Documentation — Platform v7.1.2 · Telegram Bot API 10.5*

Stop one person from joining your bot with many Telegram accounts. `libs.IPVerify` sends the user a **Verify** button; it opens a Telegram Mini App that proves who the user is and checks how many accounts of your bot already verified from the same internet connection. You choose the limit. VPNs, proxies and Tor are refused unless you allow them.

Useful for referral, airdrop, giveaway and earning bots, where fake accounts take rewards from real users.

---

## Quick start

Ask for verification in `/start`:

```python
if libs.IPVerify.is_verified():
    bot.sendMessage("Welcome back!")
else:
    libs.IPVerify.send("Verify your account to continue.", then="/verified", max_per_ip=1)
```

When the check is done, Telebot Creator runs your `then` command for that user. Read the result there:

```python
check = libs.IPVerify.status()
if check["verified"]:
    bot.sendMessage("✅ You're verified. Welcome!")
elif check["reason"] == "vpn":
    bot.sendMessage("❌ Turn off your VPN or proxy, then tap Verify again.")
else:
    bot.sendMessage("❌ Too many accounts already verified from your internet connection. Use your own connection and send /start again.")
```

The user sees a page inside Telegram with a **Tap to verify** button. One tap runs the check, then the page shows **You're verified**, **Turn off your VPN** or **Too many accounts on this network**, with a button to try again or go back to your bot.

---

## Your own button

`link()` returns the address only, for a keyboard you build yourself. Put it in a `web_app` button so Telegram opens it as a Mini App:

```python
url = libs.IPVerify.link(then="/verified", max_per_ip=2)
bot.sendMessage("Verify first:", reply_markup={"inline_keyboard": [[{"text": "✅ Verify", "web_app": {"url": url}}]]})
```

---

## Reference

| Function | Arguments | Returns |
|---|---|---|
| `libs.IPVerify.send(text, button, then, max_per_ip, require_telegram, chat_id, block_vpn)` | `text`: message above the button (HTML). `button`: label, default "✅ Verify". `then`: your command to run once the check is done. `max_per_ip`: accounts allowed per connection, 1 – 1000, default 1. `require_telegram`: default `True`. `chat_id`: default the user. `block_vpn`: refuse VPNs, proxies and Tor, default `True` | `True` when the message was sent |
| `libs.IPVerify.link(then, max_per_ip, require_telegram, block_vpn)` | Same as above | The verification address, or `None` outside a command |
| `libs.IPVerify.status(user)` | `user`: user id, default the current user | A dict, see below |
| `libs.IPVerify.is_verified(user)` | `user`: user id, default the current user | `True` or `False` |
| `libs.IPVerify.reset(user)` | `user`: user id, default the current user | `True` when there was a check to forget |

`status()` returns:

```python
{"verified": True, "status": "passed", "reason": None, "accounts_on_network": 1, "limit": 1,
 "country": "IN", "method": "miniapp", "time": "2026-11-28T10:00:00Z"}
```

`status` is `"passed"`, `"refused"` or `"none"` (never checked). For a refused user, `reason` says why: `"vpn"` (a VPN, proxy, Tor or server connection) or `"limit"` (the connection already has `max_per_ip` accounts). Inside the `then` command the same dict is also in `options["ipverify"]`.

---

## How it works

1. The user taps **Verify** in your bot. Telegram opens the page as a Mini App and gives it a signed proof of the user's account (Telegram's `initData`).
2. The user taps **Tap to verify**. Telebot Creator checks that signature with your bot's token, so the page cannot be faked or opened for someone else.
3. A VPN, proxy or Tor connection is refused (unless `block_vpn=False`).
4. It counts how many accounts of **your bot** already passed from the same connection. Below the limit, the user passes; at the limit, the user is refused.
5. Your `then` command runs for that user, once for each new result.

A user who passed stays verified, even on another connection later. A refused user can try again, for example after turning off the VPN or from another connection, and pass there.

## VPNs and proxies

VPNs are refused by default: with a VPN, one person could verify many accounts from different addresses. Telebot Creator refuses connections from known VPN providers, Tor, and servers in data centers (where proxies run). The user sees **Turn off your VPN** and a **Try again** button.

To accept VPN users, pass `block_vpn=False`:

```python
libs.IPVerify.send("Verify your account to continue.", then="/verified", max_per_ip=1, block_vpn=False)
```

## Privacy

- **The bot owner never sees the user's IP address.** Telebot Creator keeps only a coded fingerprint of the connection, different for every bot, so one bot's records say nothing about another's.
- The page also notes the user's country, shown in `status()`.

---

## Notes & Requirements

- **Private chats.** A Mini App button works only in a private chat with the bot. In a group, `send()` uses a normal link that opens in the browser; for that, pass `require_telegram=False` (weaker: the user is then known by the link alone, not by Telegram's signature).
- **Shared connections.** Some mobile networks put many customers behind one address, and family members share a home Wi-Fi. If honest users get refused, raise `max_per_ip` to 2 or 3. IPv6 connections count per household or phone (the `/64` network).
- **Let a user try again.** `libs.IPVerify.reset(user_id)` forgets a user's check and frees their place on that connection.
- **Links expire** after 24 hours, and a user can get at most 30 links an hour. Create the link when you send it.
- **VPN detection** uses address lists kept on Telebot Creator's servers; the address is never sent to another service. A VPN that is not on the lists yet can pass, and a refused VPN user does not use up a place on any connection.
- The limit is the one in the link the user opened, so a new `max_per_ip` applies to new links. Users who already passed stay passed.
