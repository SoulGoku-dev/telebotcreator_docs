---
description: >-
  Telebot Creator security notice, October 2026: what was accessed, what data was
  copied, the action every creator needs to take, and what we have changed.
---

# Security Notice — October 2026

*Published 18 October 2026. This page is the authoritative record of this incident and
will be updated if anything material changes.*

## Summary

In early October 2026 an unauthorized party gained access to one of our servers and
copied data from our database. They defeated our bot-code sandbox by exploiting the way the
Python language exposes its own standard library, and chained that into further
vulnerabilities to reach the server. We detected and removed their access on 16 October,
closed the exploit chain end to end, and have rotated the credentials involved.

**We have already taken the protective steps that were ours to take:**

- Every API key on the platform has been **reset**. Your old key no longer works — collect the new one from your panel. If an external script of yours stopped working, this is why.
- All stored passwords have been converted to **one-way hashing**. This protects them from here on; it cannot undo a copy already taken, which is why the step below still matters.
- **You will be asked to set a new password the next time you sign in.** This applies to 71,181 accounts.

**There are two things we cannot do for you, and they need you:**

1. **Set a new password** when prompted — and if you used that same password on another service, change it there as well.
2. **If you wrote a secret inside your bot's code** — a Telegram token, an API key, a crypto wallet key — **replace it.** Code was among the data copied, so anything written inside it should be considered known to someone else.

The rest of this notice sets out what was copied, what was not, and what we have changed.

---

## What happened

An access credential that we did not create was added to one of our servers at some point
after **30 September**. It was named to resemble our own, which is why it was not spotted
immediately. It was used between **11 and 13 October**, and we found and removed it on
**16 October**.

While it was active, the party behind it had administrative access to that server, and
used it to run database export commands. We confirmed this from our database's own
records.

On **17 October** the same party returned through a different route and began transferring
bot source code. We detected that transfer while it was in progress and cut their access
within minutes.

### How they got in

Bot code on Telebot Creator does not run freely. It runs inside a sandbox, every command
is put through an automated review that rejects the known ways of breaking out of one, and
creators work under limits on how many bots and commands they can create and how large
each can be. Those controls were in place throughout. They are the reason this required a
deliberate and novel effort rather than a simple one.

**The sandbox was escaped using Python itself.** The route was not a mistake in our code.
It is a property of the language: a Python module exposes the other modules it imports as
ordinary attributes, so code permitted to use one standard-library module — the
regular-expression module, in this instance — can walk from it to the modules behind it and
reach system functionality that was never meant to be within its reach. Our automated
review did not recognise the technique, because nobody had used it against us before.
Isolating untrusted Python inside the same process is a problem the wider Python community
regards as extremely difficult, for precisely this reason.

**The exploit then chained into further vulnerabilities to reach the server.** This is
important: **we did not grant anyone access**. Every step of this chain was a technical
exploit the attacker had to find and execute. The specific chain used Python's object
model to walk from the sandbox out to privileged system functionality — a known-hard class
of problem that the mainstream approach to untrusted Python addresses by isolating code in
operating-system containers or separate processes, which is where we have now moved.

What the exploit chain reached, once outside the sandbox, let it install an access
credential and read our server-side configuration. The at-rest encryption on that
configuration was not weak in itself; what the chain reached meant the keys protecting it
were in the same place. We treat that reach as the architectural boundary that failed to
contain the escape, and have rebuilt it: bot execution today runs in a completely
unprivileged, isolated context — an escape would land on an account with no server access,
no configuration access, and nothing to install.

**The escape is confirmed to have been used against us on 17 October**, which is how the
bot source code was taken.

For the earlier access in September and October, we can tell you that the same flaw was
present and reachable throughout that period, and that the way the credential
appeared — **with no sign-in recorded, no command history, and written into the middle of an
existing file** — is the signature of a program writing it rather than a person signing in.
We have **not** conclusively proven that this flaw was the route used on that occasion,
because the records that would settle it were deleted. We are telling you our strongest
finding and its limit, rather than presenting one as the other.

The exploit route and every related one found in the review are now closed, and bot
execution has been fully isolated — an escape we have not yet thought of would land in an
environment with no access to the server or to our configuration.

We examined and ruled out our hosting control panel and our second server as the route.

---

## What was copied

### Bot source code

| | |
|---|---|
| Bot command records | **8,446,085** |
| Bots affected | **186,768** |
| Creator accounts affected | **22,902** |
| Share of all stored commands | about 39% |

Each record contains one bot command and its source code. If your bot is in this set,
somebody else has a copy of how it works — **and of anything written inside it.**

Our own scan of that code found secrets that creators had typed directly into it:

| | |
|---|---|
| Telegram bot tokens | **2,516 distinct bots** |
| Crypto wallet keys | 4,273 occurrences |
| Other keys and secrets | 8,619 occurrences |

**2,562 accounts have at least one high-risk secret inside their bot code.** Those
creators are being emailed individually. Of 382 creators known to keep a wallet key in
their code, **148 are in the copied set.**

### Bot records

Our bot table holds the bot id, name, username, owner, user count and the **bot's Telegram
token**. It contains 423,914 records in total, which includes stopped, deleted and
duplicated bots — that figure is the size of the table, not a count of live tokens
exposed. The safe assumption is simpler: **if a bot of yours existed in early October,
replace its token.**

### Your bots' end users

Our end-user table holds 60,958,113 records and **at least 2.16 million were exported.**
Each record contains:

| Field | What it is |
|---|---|
| Telegram numeric ID | the identifier Telegram assigns a user |
| Bot id | which of your bots they used |
| Owner email | your email address |
| Language code | their Telegram language setting |
| Dates | when they first used the bot, and when they were last active |
| Status | active or inactive |

**What is not in it:** no names, no usernames, no phone numbers, no email addresses, and
**no message content**. A Telegram numeric ID does not allow anyone to read a person's
messages or access their account. It does allow somebody operating a bot to message them,
so your users could receive unsolicited messages referring to your bot.

If you have your own obligation to notify your users, contact support and we will give you
the affected user count for each of your bots.

### Account records

Our creator account table holds email address, password, API key, plan, points balance and
account dates, across 71,895 accounts.

**On passwords.** Passwords were encrypted at rest. The encryption itself was not broken;
what we cannot rule out is that an attacker who reached the server could have reached the
key that unlocks it, which is a different thing but the same practical risk to you. We are
therefore treating every password as potentially known, which is why all passwords have
since been moved to one-way hashing — a form that cannot be reversed even by somebody
holding the entire server — and why you will be asked to set a new one. Please treat the
password change as necessary rather than precautionary, and change it anywhere else you
used it.

**On API keys.** API keys were held in a form the attacker could read with the database in
hand. **All 71,895 have been reset**, and the new keys are stored one-way hashed — unreadable
even with full database access.

### Other services

Several other projects share the same database server. Their operators have been notified
directly so they can review their own position and inform their users as they see fit.
Those are not ours to speak for.

---

## What was not copied

The party staged a further 12 GB of data on 17 October and **none of it was sent.** Their
transfer failed repeatedly, and their attempt to export our full 60-million-row end-user
table could not run, because we had already removed the tools it needed.

Confirmed not transferred on 17 October: the complete account table, our advertising
records, and the full end-user table.

**One caveat we will not leave out.** For the period in October when they held
administrative access, they had the ability to read data directly. We found no evidence
that they read more than is described above, and we cannot prove a negative. We would
rather tell you that than imply a certainty we do not have.

---

## What you should do

**1. Set a new password when prompted.** If you used the same password elsewhere, change it
there too. This is the most important item here.

**2. Replace your bots' Telegram tokens.** In @BotFather: select your bot → API Token →
Revoke current token. Then paste the new token into Telebot Creator.

**3. If a crypto wallet key is written in your bot code, move the funds to a new wallet.**
Changing the key is not enough on its own — whoever holds the old key can still reach the
old wallet. Move the money.

**4. Replace any other API key or password written in your bot code**, at the service it
belongs to.

**5. Your Telebot Creator API key is already reset.** Collect the new one from your panel
if an integration of yours has stopped.

**On where to keep secrets.** Secrets written directly into bot code were the single
largest source of real risk in this incident, and we are not going to pretend we have a
safe place to put them instead. Our existing data storage keeps values out of your code,
but it lives in the same database, so it would not have protected you here either.

Our honest guidance for now: **keep high-value secrets off the platform entirely.** Have
your bot call a service you control and hold the key there. We are building proper
secret storage, with encryption that is separate from the database, and we will tell you
when it is ready rather than before.

---

## What we have changed

- The unauthorized credential has been removed, and credentials of that kind are now held where they cannot be altered without raising an alert.
- The database account used in the exports has been **replaced**, and its credential moved out of reach of the application.
- Signing keys and application keys have been rotated.
- All API keys reset; **no readable API key remains** anywhere in our systems.
- All passwords converted to one-way hashing. We cannot read them, and neither can anyone who obtains the database.
- The weakness in the bot-code system used on 17 October has been **closed**, together with every related weakness found in the review that followed. Every stored bot command — several million — was re-checked.
- Bulk data extraction is no longer possible by the means used against us. This is what stopped the attempt on the full end-user table on 17 October.
- Additional monitoring and alerting has been put in place.
- Access over anonymity networks is now blocked across the site and the API, because the October access was conducted that way.
- Bot execution has been moved into a fully unprivileged, isolated environment — an escape today reaches an account that can do nothing with it.
- A review for tampering found **no modified software and nothing left behind** on our systems.

Further work is in progress, including stricter sign-up verification and a migration to
rebuilt infrastructure.

---

## What we still do not know

Three things, stated rather than glossed over.

**Which account ran it.** We know the route — the flaw described above — but not whose bot
code used it. That flaw was reachable by any creator's bot code. The code that installed
the credential was either run once without being saved, or erased afterwards; whoever did
this had full control of the server and did delete other records. We have identified and
acted on accounts that independently built the same kind of break-out, but we cannot prove
that any one of them was used here, and we will not name anyone on a guess.

**Who was behind it.** Not established. Doing this required nothing more than a free
account and the code to exploit the flaw.

**Whether anything else was read.** For the days the exploit chain granted them access
to the server, the attacker had the ability to read data directly. What is described in
this notice is what the evidence proves was read. We cannot prove the negative, and we
are not going to claim it.

We have closed every access route we identified. We are not going to tell you the
environment is now guaranteed secure — the 17 October event happened after we had removed
the original credential, through a different weakness, and that is exactly the kind of
assurance we are no longer willing to give. We will publish an update if anything material
changes.

---

## Accounts we have banned

Following this incident we reviewed bot code across the platform and acted on every
account where we found evidence of an attack on our systems.

| | |
|---|---|
| Banned on evidence of attacking the platform | **18 accounts** |
| Banned under our anonymity-network policy | **15 accounts** |
| Suspended during review and then **reinstated** | **25 accounts** |

**The 18.** Each is tied to specific evidence in our records: breaking out of the system
that isolates bot code, running operating-system commands on our servers, reading
privileged files, building unauthorized program-hosting services on our infrastructure, or
harvesting credentials. Two of them carried out the 17 October transfer of bot source
code.

**None of the 18 is the holder of the unauthorized server credential.** That party did not
need an account with us. Their identity is not established.

**The 15.** Access over anonymity networks is now blocked across the platform, because the
October access was conducted that way. These accounts had previously signed in that way and
were banned under that policy. **They are not accused of attacking the platform** — we
examined each one and found no attack code and no connection to the incident. If you use
such a network for privacy rather than abuse and have lost access, contact support and we
will review your account.

**The 25 we reinstated.** We initially suspended 25 further accounts and then reversed
every one. Their code resembled an attack to our automated checks but was not — in most
cases creators working around a limitation of our platform, and in one case a creator's own
security filter being mistaken for the thing it was filtering. Those reversals affected
roughly **197,000 end users**.

We mention the reversals because a wrong suspension is a failure too, and because you are
entitled to know that we verify before we accuse. If your account has been actioned and
you believe it is wrong, contact support — we will review it and we have reversed
decisions before.

Thirteen older bans, unrelated to this incident, are not counted here.

Account identifiers and the supporting evidence are retained confidentially and have been
provided to our legal counsel.

---

## Law enforcement

This matter has been referred to **federal law enforcement** and to our **legal counsel.**
We have preserved the complete evidence record and will cooperate fully with any
investigation.

**If you have suffered a financial loss connected to this incident, contact support.** We
will provide the evidence you need for your own report or claim.

---

## Support

| | |
|---|---|
| Telegram | **@NexoNodex** |
| Telegram | **@pluto_ad** |

Please contact us if you need:

- to know whether **your bots** were among those whose code was copied
- the **affected end-user count for each of your bots**, for your own records or notifications
- help replacing a token or key, or recovering access to your account
- a ban reviewed
- evidence for a loss report

---

## Closing

**We did not grant anyone access to our systems.** Every step of this attack was an
exploit the attacker had to find and execute against security that was in place: an
isolating sandbox, an automated code reviewer that rejects known escape techniques, hard
limits on how much any one account could do, and rate limits across the platform. The
first step defeated them using a property of the Python language that the wider community
regards as very hard to defend against inside a single process. The follow-on steps
chained through additional vulnerabilities to reach the server.

That is not a story about a lack of security. It is a story about a determined attacker
finding a novel chain through security that was there. We take neither of those lightly,
and we have strengthened every layer in response: bot execution is now fully isolated
from any privileged context on our servers, passwords are one-way hashed, API keys are
one-way hashed, and the specific exploit chain and every related one is closed.

We have chosen to publish the real figures, including the ones that do not flatter us —
the 197,000 users behind suspensions we got wrong, and the limits of what our evidence
can prove.

We have chosen to publish the real figures, including the ones that do not flatter
us — the 197,000 users behind suspensions we got wrong, and the limits of what our evidence
can prove.

What we ask of you is short: **set a new password when prompted, replace your bot tokens,
and if you ever put a wallet key inside your bot's code, move those funds today.**

— The Telebot Creator team
