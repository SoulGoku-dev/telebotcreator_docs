# Telebot Creator API v2 - Flexible Authentication Documentation

### Overview

This document describes the flexible authentication system implemented for the Telebot Creator API v2. The system supports both JWT cookie-based authentication and API key authentication, allowing users to choose the most appropriate method for their use case.

### Authentication Methods

#### 1. JWT Cookie Authentication (Web Interface)

Used by the web interface for user sessions.

* **Cookie Name**: `login_token`
* **Algorithm**: HS256
* **Expiration**: 6 hours (default) or 30 days (remember me)

#### 2. API Key Authentication (Programmatic Access)

Used for programmatic access to the API.

* **Header**: `Authorization: Bearer <api_key>`
* **Query Parameter**: `?api_key=<api_key>`
* **JSON Body**: `{"api_key": "<api_key>"}`

### Endpoints with Flexible Authentication

The following endpoints support both authentication methods:

#### Bot Management

**Create Bot**

* **Endpoint**: `POST /v2/create-bot`
* **Description**: Creates a new bot with the provided token and name
*   **Request Body**:

    ```json
    {
      "bot_token": "1234567890:AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA",
      "bot_name": "My Bot"
    }
    ```
*   **Response**:

    ```json
    {
      "ok": true,
      "result": {
        "botid": "generated_bot_id",
        "bot_name": "My Bot",
        "bot_username": "mybotusername"
      }
    }
    ```

**Delete Bot**

* **Endpoint**: `DELETE /v2/bots/{botid}`
* **Description**: Soft deletes a bot (moves to deleted\_bots collection)
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
*   **Response**:

    ```json
    {
      "ok": true,
      "result": "Bot deleted successfully"
    }
    ```

**Update Bot Token**

* **Endpoint**: `POST /v2/bots/{botid}/update-bot-token`
* **Description**: Updates the bot's Telegram token
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
*   **Request Body**:

    ```json
    {
      "token": "1234567890:NEWAAAAAAAAAAAAAAAAAAAAAAAAAAAA"
    }
    ```
*   **Response**:

    ```json
    {
      "ok": true,
      "result": "Bot token updated successfully"
    }
    ```

**Update Bot Version**

* **Endpoint**: `POST /v2/bots/{botid}/version`
* **Description**: Updates the bot's telebot library version
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
*   **Request Body**:

    ```json
    {
      "version": "4.19.0"
    }
    ```
*   **Response**:

    ```json
    {
      "ok": true,
      "result": "Bot version updated successfully"
    }
    ```

#### Command Management

**Create Command**

* **Endpoint**: `POST /v2/bots/{botid}/commands`
* **Description**: Creates a new command for the bot
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
*   **Request Body**:

    ```json
    {
      "command": "hello",
      "code": "bot.reply_to(message, 'Hello World!')"
    }
    ```
*   **Response**:

    ```json
    {
      "ok": true,
      "result": "Command created successfully"
    }
    ```

**Update Command**

* **Endpoint**: `PUT /v2/bots/{botid}/commands/{command}`
* **Description**: Updates an existing command's code
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
  * `command`: Base64 encoded command name
*   **Request Body**:

    ```json
    {
      "code": "bot.reply_to(message, 'Updated Hello World!')"
    }
    ```
*   **Response**:

    ```json
    {
      "ok": true,
      "command": "hello"
    }
    ```

**Get Command**

* **Endpoint**: `GET /v2/bots/{botid}/commands/{command}`
* **Description**: Retrieves a specific command's details
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
  * `command`: Base64 encoded command name
*   **Response**:

    ```json
    {
      "ok": true,
      "command": {
        "command": "hello",
        "code": "bot.reply_to(message, 'Hello World!')",
        "is_pinned": false
      }
    }
    ```

**Delete Command**

* **Endpoint**: `DELETE /v2/bots/{botid}/commands/{command}`
* **Description**: Deletes a command from the bot
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
  * `command`: Base64 encoded command name
*   **Response**:

    ```json
    {
      "ok": true,
      "result": "Command deleted successfully"
    }
    ```

**Rename Command**

* **Endpoint**: `PATCH /v2/bots/{botid}/commands/{command}/rename`
* **Description**: Renames an existing command
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
  * `command`: Base64 encoded current command name
*   **Request Body**:

    ```json
    {
      "new_name": "greet"
    }
    ```
*   **Response**:

    ```json
    {
      "ok": true,
      "result": "Command renamed successfully"
    }
    ```

#### Folder Management

**Get Folders**

* **Endpoint**: `GET /v2/bots/{botid}/commands/folders`
* **Description**: Retrieves all folders for a bot
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
*   **Response**:

    ```json
    {
      "ok": true,
      "folders": [
        {
          "name": "utility",
          "commands": ["help", "start"]
        }
      ]
    }
    ```

#### Data Management

**Export Users File**

* **Endpoint**: `POST /v2/export-users-file/{botid}`
* **Description**: Exports bot users data in specified format
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
*   **Request Body**:

    ```json
    {
      "format": "json",
      "include_creation_date": true,
      "include_last_active_date": true
    }
    ```
* **Response**: File download with users data

**Export Bot**

* **Endpoint**: `GET /v2/bots/{botid}/export-bot`
* **Description**: Exports bot configuration and commands
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
* **Query Parameters**:
  * `format`: `json`, `yaml`, or `txt` (default: `json`)
  * `include_data`: `true` or `false` (default: `false`)
* **Response**: File download with bot data

**Import Commands**

* **Endpoint**: `POST /v2/bots/{botid}/import-commands`
* **Description**: Imports commands from exported data
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
*   **Request Body**:

    ```json
    {
      "import_data": "base64_encoded_data",
      "format": "json",
      "remove_old_commands": false
    }
    ```
*   **Response**:

    ```json
    {
      "ok": true,
      "result": "Commands imported successfully"
    }
    ```

#### Bot Operations

**Transfer Bot**

* **Endpoint**: `POST /v2/bots/{botid}/transfer-bot`
* **Description**: Transfers bot ownership to another user
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
*   **Request Body**:

    ```json
    {
      "to_email": "recipient@example.com"
    }
    ```
*   **Response**:

    ```json
    {
      "ok": true,
      "result": "Bot transferred successfully"
    }
    ```

**Clone Bot**

* **Endpoint**: `POST /v2/bots/{botid}/clone-bot`
* **Description**: Creates a copy of the bot with optional new token
* **URL Parameters**:
  * `botid`: The unique identifier of the bot
*   **Request Body**:

    ```json
    {
      "new_token": "1234567890:CLONEDAAAAAAAAAAAAAAAAAAAAAAA"
    }
    ```
*   **Response**:

    ```json
    {
      "ok": true,
      "result": {
        "botid": "new_bot_id",
        "message": "Bot cloned successfully"
      }
    }
    ```

### Command Name Encoding

For endpoints that require a command name in the URL path, the command name must be Base64 encoded to handle special characters safely.

#### Encoding Process

1. **Original Command**: `/start`
2. **URL-Safe Base64 Encoding**: `L3N0YXJ0`
3. **Final URL**: `/v2/bots/{botid}/commands/L3N0YXJ0`

#### Examples

| Original Command | Base64 Encoded     |
| ---------------- | ------------------ |
| `/start`         | `L3N0YXJ0`         |
| `/help`          | `L2hlbHA=`         |
| `/settings`      | `L3NldHRpbmdz`     |
| `hello_world`    | `aGVsbG9fd29ybGQ=` |
| `user info`      | `dXNlciBpbmZv`     |

#### Code Examples

**JavaScript/Node.js**

```javascript
function encodeCommand(command) {
    return Buffer.from(command, 'utf8').toString('base64');
}

function decodeCommand(encoded) {
    return Buffer.from(encoded, 'base64').toString('utf8');
}

// Usage
const encoded = encodeCommand('/start'); // "L3N0YXJ0"
const decoded = decodeCommand('L3N0YXJ0'); // "/start"
```

**Python**

```python
import base64

def encode_command(command):
    return base64.b64encode(command.encode('utf-8')).decode('ascii')

def decode_command(encoded):
    return base64.b64decode(encoded.encode('ascii')).decode('utf-8')

# Usage
encoded = encode_command('/start')  # "L3N0YXJ0"
decoded = decode_command('L3N0YXJ0')  # "/start"
```

**cURL Examples**

```bash
# Get a command (command name: /start)
curl -X GET "https://api.telebotcreator.com/v2/bots/mybotid/commands/L3N0YXJ0" \
  -H "Authorization: Bearer your_api_key_here"

# Update a command
curl -X PUT "https://api.telebotcreator.com/v2/bots/mybotid/commands/L3N0YXJ0" \
  -H "Authorization: Bearer your_api_key_here" \
  -H "Content-Type: application/json" \
  -d '{"code": "bot.reply_to(message, \"Hello from API!\")"}'

# Delete a command
curl -X DELETE "https://api.telebotcreator.com/v2/bots/mybotid/commands/L3N0YXJ0" \
  -H "Authorization: Bearer your_api_key_here"
```

### Authentication Examples

#### Using Bearer Token in Header

```bash
curl -X GET "https://api.telebotcreator.com/v2/bots/mybotid/commands" \
  -H "Authorization: Bearer your_api_key_here"
```

#### Using API Key as Query Parameter

```bash
curl -X GET "https://api.telebotcreator.com/v2/bots/mybotid/commands?api_key=your_api_key_here"
```

#### Using API Key in JSON Body

```bash
curl -X POST "https://api.telebotcreator.com/v2/bots/mybotid/commands" \
  -H "Content-Type: application/json" \
  -d '{
    "api_key": "your_api_key_here",
    "command": "test",
    "code": "bot.reply_to(message, \"Test command\")"
  }'
```

### Error Responses

#### Authentication Errors

```json
{
  "ok": false,
  "result": "Authentication required"
}
```

#### Invalid API Key

```json
{
  "ok": false,
  "result": "Invalid API key"
}
```

#### Bot Not Found

```json
{
  "ok": false,
  "result": "Bot not found or not owned by you"
}
```

#### Command Not Found

```json
{
  "ok": false,
  "result": "Command not found"
}
```

#### Validation Errors

```json
{
  "ok": false,
  "result": "Invalid bot token format"
}
```

### Rate Limiting

The API implements rate limiting to prevent abuse:

* **Rate Limit**: 60 requests per minute per IP
* **Headers**: Rate limit information is provided in response headers
  * `X-RateLimit-Limit`: Maximum requests per window
  * `X-RateLimit-Remaining`: Remaining requests in current window
  * `X-RateLimit-Reset`: Time when the rate limit resets

### Best Practices

1. **Security**
   * Store API keys securely and never expose them in client-side code
   * Use HTTPS for all API requests
   * Rotate API keys regularly
2. **Error Handling**
   * Always check the `ok` field in responses
   * Implement proper retry logic for transient errors
   * Log errors for debugging purposes
3. **Performance**
   * Respect rate limits to avoid being temporarily blocked
   * Use batch operations when available
   * Cache responses when appropriate
4. **Command Encoding**
   * Always Base64 encode command names for URL paths
   * Use URL-safe Base64 encoding
   * Handle encoding/decoding errors gracefully

### Support

For additional support or questions about the API:

* Check the error messages for specific guidance
* Review this documentation for proper usage
* Contact support through the official channels

***

_Last updated: 2025-07-20_
