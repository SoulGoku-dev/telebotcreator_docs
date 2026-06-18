# Telegram Bot API 10.1 Update

*Telebot Creator Documentation — Platform v7.1.2 · Telegram Bot API 10.1*

*Released: June 2026*

Telebot Creator's `bot` object now tracks **Telegram Bot API 10.1**, jumping from the previous 7.x baseline through Bot API 8.x, 9.x, and 10.x. Every newly supported method is available in the TPY sandbox in both **camelCase** and **snake\_case**, exactly like the existing Telegram methods.

> **Two version numbers.** This release changes the **Telegram Bot API level** (now **10.1**) — how much of Telegram's official API the `bot` object exposes. It is independent of the **TBC platform version** (currently **7.1.2**), which tracks the Telebot Creator product itself.

---

## What's New

Telegram shipped a large run of features across API 8.x–10.1 — Gifts, Stories, full Business-account management, Checklists, Suggested posts, organization Verification, and an expanded Stars economy. All of them are now callable directly from your commands.

### 🎁 Gifts

Send gifts, gift Premium subscriptions, and manage received gifts.

| Method | Purpose |
|---|---|
| `getAvailableGifts` | List the gifts the bot can send. |
| `sendGift` | Send a gift to a user or channel chat. |
| `giftPremiumSubscription` | Gift a Telegram Premium subscription (paid in Stars). |
| `getUserGifts` | List gifts received by a user. |
| `convertGiftToStars` | Convert a received gift back into Stars. |
| `upgradeGift` | Upgrade a regular gift to a unique gift. |
| `transferGift` | Transfer an owned unique gift to another chat. |

```python
# List available gifts and send one to the current user
gifts = bot.getAvailableGifts()
if gifts and gifts.gifts:
    bot.sendGift(gift_id=gifts.gifts[0].id, user_id=u, text="Thanks for being here! 🎁")
```

### 📖 Stories

Post, edit, and delete stories on behalf of a connected business account.

| Method | Purpose |
|---|---|
| `postStory` | Post a story (requires `active_period`). |
| `editStory` | Edit an existing story. |
| `deleteStory` | Delete a posted story. |

### 💼 Business Accounts

Manage a Telegram Business account that a user has connected to your bot via a `business_connection_id`.

| Method | Purpose |
|---|---|
| `readBusinessMessage` | Mark an incoming business message as read. |
| `setBusinessAccountName` | Set the account's name. |
| `setBusinessAccountUsername` | Set the account's username. |
| `setBusinessAccountBio` | Set the account's bio. |
| `setBusinessAccountProfilePhoto` | Set the account's profile photo. |
| `removeBusinessAccountProfilePhoto` | Remove the account's profile photo. |
| `setBusinessAccountGiftSettings` | Configure which gift types are accepted. |
| `getBusinessAccountStarBalance` | Read the account's Star balance. |
| `transferBusinessAccountStars` | Transfer Stars from the account to the bot. |

### ✅ Checklists

| Method | Purpose |
|---|---|
| `sendChecklist` | Send a checklist message (business accounts). |
| `editMessageChecklist` | Edit an existing checklist message. |

### 📝 Suggested Posts

| Method | Purpose |
|---|---|
| `approveSuggestedPost` | Approve a suggested post in a direct-messages channel. |
| `declineSuggestedPost` | Decline a suggested post (with an optional comment). |

### 🛡️ Verification

| Method | Purpose |
|---|---|
| `verifyUser` | Verify a user on behalf of an organization. |
| `verifyChat` | Verify a chat on behalf of an organization. |
| `removeUserVerification` | Remove a user's verification. |
| `removeChatVerification` | Remove a chat's verification. |

### ⭐ Stars

| Method | Purpose |
|---|---|
| `getMyStarBalance` | Read the bot's own Star balance. |
| `editUserStarSubscription` | Cancel or re-enable a user's Star subscription. |

```python
balance = bot.getMyStarBalance()
bot.sendMessage(f"Current Star balance: {balance.amount}")
```

### 😀 Reactions

| Method | Purpose |
|---|---|
| `deleteMessageReaction` | Remove a reaction from a message. |
| `deleteAllMessageReactions` | Remove all reactions from a message. |

### ✨ TBC-Custom Methods

Two helpers unique to Telebot Creator complement the official API:

| Method | Purpose |
|---|---|
| `sendRichMessage` | Send a rich (structured) message. |
| `sendLivePhoto` | Send a live photo. |

---

## Notes & Requirements

- **Business connection required.** Gift, Story, and Business-account methods act on a user account that has been linked to your bot. They require a `business_connection_id` obtained from that connection. `getAvailableGifts`, `sendGift`, and `getMyStarBalance` work with the bot's own token.
- **Naming.** Every method works in camelCase (`bot.getUserGifts(...)`) and snake\_case (`bot.get_user_gifts(...)`).
- **Media parameters.** As everywhere in TBC, pass a Telegram `file_id`, a public URL, or a structured `dict`. Local file uploads are not available in the sandbox.
- **Backward compatible.** Existing bot code is unaffected. Restart your bot to pick up the new methods.

See the full argument tables in the [TPY Language Reference](tpy-language-reference.md) (section 4.4.3 — Telegram Bot API 8.x – 10.1 Methods).
