# Telebot Creator MCP Server

The **Telebot Creator MCP** is a public, free [Model Context Protocol](https://modelcontextprotocol.io)
server that gives any AI agent live access to the complete Telebot Creator
documentation — and, paired with your own API key, the ability to **build and
deploy real bots**.

```
URL:    https://api.telebotcreator.com/v2/mcp
Auth:   none (public, free, read-only docs)
Cost:   free for everyone
```

## Add it to your agent

**Claude (Desktop / claude.ai / API connector):** add a custom connector with the
URL above. No key, no sign-up.

**Any MCP client:** point it at `https://api.telebotcreator.com/v2/mcp` (Streamable
HTTP / JSON-RPC, CORS open).

## What the MCP gives you (tools)

| Tool | What it does |
|---|---|
| `list_docs` | List every documentation page (filename, title, size). |
| `search_docs` | Search the docs for a term; returns snippets with page + section. |
| `get_doc` | Fetch one page by name (e.g. `tpy-language-reference`, `agent-build-guide`). |
| `get_full_docs` | The entire documentation in one shot (bulk ingestion). |

Key pages an agent should pull: **`agent-build-guide`** (build + deploy workflow),
`tpy-language-reference`, `tbc-libraries-libs`, `command-in-tpy`.

## Use the MCP to MAKE BOTS

The MCP itself is read-only docs — but it teaches the agent the **exact workflow
and API** to build bots. With **your own Telebot Creator API key**, an agent can:

- **Make a bot** — `POST /v2/create-bot`
- **Create a command** — `POST /v2/bots/{botid}/commands`
- **Edit a command** — `PUT /v2/bots/{botid}/commands/{command}`
- **Deploy many commands at once** — `POST /v2/bots/{botid}/import-commands`
  (additive by default — updates + adds, **deletes nothing**)

All of these authenticate with `Authorization: Bearer <YOUR_TBC_API_KEY>`. It's
**easy and safe**: imports are additive, so an agent can iterate without ever
wiping your existing commands (deletion only happens on an explicit full rewrite).

### 🔑 API-key rule for agents (important)

1. The agent should **look for the key in the project's `.env`** as `TBC_API_KEY`.
2. **If it's not there, the agent must ASK you for your Telebot Creator API key**
   before any create / edit / deploy. It must never invent or guess a key.
3. You get the key from the TBC dashboard → account / API settings.

The MCP (docs) needs **no key**. The key is only for the *actions* above, which
hit the REST API — so you stay in full control of what gets created or changed.

## The recommended build flow

See **`get_doc agent-build-guide`** for the full, safe workflow: author commands
locally (one file per command + a manifest), build an `import_ready.json`, then
push it additively to your bot. The agent decides which commands to include and
never deletes anything unless you explicitly ask for a total rewrite.
