# TPY API Reference — `Bot`, `Account`, `User`

> **Auto-generated** from `commandHandler.py` `_SAFE_METHODS` — the authoritative
> allow-list of methods TPY code may call. Do not edit by hand; regenerate on deploy
> so it can never drift from the code again.

> Generated: 2026-07-08

> Coverage: **2/82** methods have a code docstring; 80 still need one.


---

## `Bot.*` — 32 methods

### `Bot.Transfer(email: str, bot_id: str, bot_token: Optional[str] = None, run_now: Optional[bool] = False) -> dict`

_(no docstring in code — needs a one-line description)_

### `Bot.blockUser(user_id: str)`

_(no docstring in code — needs a one-line description)_

### `Bot.broadcast(code=None, command=None, callback_url=None, bot_id=None, api_key=None, function=None, warnings=None, mode=None, bot_ids=None, speed=None, **kwargs)`

Broadcast to users.

Parameters
----------
mode : str, optional
    "single" (default) — broadcast to the current bot's users only.
    "multi"  — broadcast to users of up to 500 specified bots (bot_ids required).
    "all"    — broadcast to users of ALL bots owned by the same email (min 20 members each).
bot_ids : list, optional
    List of bot ID strings (required when mode="multi", max 500).
speed : int, optional
    Messages per second per bot, 1-25. Default 8. 25 = ~90000 msgs/hr/bot.
code / command / function / kwargs : same as before.

### `Bot.cancelScheduledTask(job_id)`

_(no docstring in code — needs a one-line description)_

### `Bot.clearBroadcast(broadcast_id: Optional[str] = None) -> str`

_(no docstring in code — needs a one-line description)_

### `Bot.deleteAllData(except_data: Optional[list] = None)`

_(no docstring in code — needs a one-line description)_

### `Bot.deleteData(name: str, bot_id: Optional[str] = None) -> dict`

_(no docstring in code — needs a one-line description)_

### `Bot.genCaptcha(mode, captcha=None)`

_(no docstring in code — needs a one-line description)_

### `Bot.genId()`

_(no docstring in code — needs a one-line description)_

### `Bot.genRandomErrorId()`

_(no docstring in code — needs a one-line description)_

### `Bot.genRandomId()`

_(no docstring in code — needs a one-line description)_

### `Bot.gen_random_id()`

_(no docstring in code — needs a one-line description)_

### `Bot.getAllData(output_format: str = 'json') -> IO[bytes]`

_(no docstring in code — needs a one-line description)_

### `Bot.getBlockedUsers()`

_(no docstring in code — needs a one-line description)_

### `Bot.getBlockedUsersCount()`

_(no docstring in code — needs a one-line description)_

### `Bot.getBlockedUsersFile()`

_(no docstring in code — needs a one-line description)_

### `Bot.getBotUsersFile(output_format: str = 'json', include_creation_date: bool = False, include_last_active_date: bool = False)`

_(no docstring in code — needs a one-line description)_

### `Bot.getBroadcastStatus(broadcast_id: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Bot.getData(name: str, bot_id: Optional[str] = None, api_key: Optional[str] = None) -> Any`

_(no docstring in code — needs a one-line description)_

### `Bot.getDataFile(name: str, output_format: str = 'txt') -> IO[bytes]`

_(no docstring in code — needs a one-line description)_

### `Bot.getStats(time_frames=None, bot_id=None, api_key=None)`

_(no docstring in code — needs a one-line description)_

### `Bot.handleNextCommand(command: str, options: Optional[Any] = None, cancel_at_command: bool = False) -> dict`

_(no docstring in code — needs a one-line description)_

### `Bot.info(bot_id=None, api_key=None)`

_(no docstring in code — needs a one-line description)_

### `Bot.runCommand(command: str, options: Optional[Any] = None) -> dict`

_(no docstring in code — needs a one-line description)_

### `Bot.runCommandAfter(timeout, command, options=None)`

_(no docstring in code — needs a one-line description)_

### `Bot.saveData(name: str, data: Any, bot_id: Optional[str] = None, api_key: Optional[str] = None) -> dict`

_(no docstring in code — needs a one-line description)_

### `Bot.setBroadcastSpeed(broadcast_id: str, speed: int) -> dict`

Change the speed of a running broadcast (1-25 msgs/sec/bot). Takes effect immediately.

### `Bot.start(bot_id: Optional[str] = None, api_key: Optional[str] = None)`

_(no docstring in code — needs a one-line description)_

### `Bot.status(bot_id, api_key)`

_(no docstring in code — needs a one-line description)_

### `Bot.stop(bot_id: Optional[str] = None, api_key: Optional[str] = None)`

_(no docstring in code — needs a one-line description)_

### `Bot.stopBroadcast(broadcast_id: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Bot.unblockUser(user_id: str)`

_(no docstring in code — needs a one-line description)_


---

## `Account.*` — 42 methods

### `Account.blockUser(user_id: Union[str, int]) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.clear_expired_bots() -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.clone_bot(botid: str, new_token: str = None) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.create_bot(bot_token: str, bot_name: str = None, bot_username: str = None) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.create_command(botid: str, command: str, code: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.deleteAllData(except_data: Optional[list] = None, include_bot_data: bool = False)`

_(no docstring in code — needs a one-line description)_

### `Account.deleteData(name: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.delete_bot(botid: str, permanent: bool = False) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.delete_command(botid: str, command: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.edit_command(botid: str, command: str, code: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.export_bot(botid: str, format: str = 'json', include_bot_data: bool = False) -> IO[bytes]`

_(no docstring in code — needs a one-line description)_

### `Account.gen_random_id() -> str`

_(no docstring in code — needs a one-line description)_

### `Account.getAllData(name: Optional[str] = None, output_format: str = 'json') -> IO[bytes]`

_(no docstring in code — needs a one-line description)_

### `Account.getBlockedUsers(botid: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.getBlockedUsersFile(botid: str = None, output_format: str = 'csv') -> IO[bytes]`

_(no docstring in code — needs a one-line description)_

### `Account.getData(name: str) -> Any`

_(no docstring in code — needs a one-line description)_

### `Account.getDataFile(name: str, output_format: str = 'txt') -> IO[bytes]`

_(no docstring in code — needs a one-line description)_

### `Account.get_blocked_users_count(botid: str = None) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_bot_data(botid: str, name: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_bot_info(botid: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_bot_stats(botid: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_bot_status(botid: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_bot_usage(botid: str, period: str = 'all') -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_bots_list() -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_bots_stats() -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_command_info(botid: str, command: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_command_list(botid: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_command_usage(botid: str, command: str, period: str = 'all') -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_deleted_bots() -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_deleted_bots_stats() -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.get_stats() -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.import_bot(import_data: dict, new_token: str = None, format: str = 'json') -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.info() -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.permanent_delete_bot(botid: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.recover_bot(botid: str, new_token: str = None) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.restart_bot(botid: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.revoke_api() -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.saveData(name: str, data: Any) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.set_bot_data(botid: str, name: str, data: Any) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.start_bot(botid: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.stop_bot(botid: str) -> dict`

_(no docstring in code — needs a one-line description)_

### `Account.unblockUser(user_id: Union[str, int]) -> dict`

_(no docstring in code — needs a one-line description)_


---

## `User.*` — 8 methods

### `User.deleteAllData(name: Optional[str] = None, old_data: bool = False, except_data: Optional[list] = None)`

_(no docstring in code — needs a one-line description)_

### `User.deleteAllDataOfUser(user: Union[str, int])`

_(no docstring in code — needs a one-line description)_

### `User.deleteData(name: str, user: Optional[str] = None, data_type: Optional[str] = None, bot_id: Optional[str] = None, api_key: Optional[str] = None) -> dict`

_(no docstring in code — needs a one-line description)_

### `User.getAllData(name: Optional[str] = None, output_format: str = 'json', old_data: bool = False) -> IO[bytes]`

_(no docstring in code — needs a one-line description)_

### `User.getAllDataOfUser(user: Union[str, int], output_format: str = 'json') -> IO[bytes]`

_(no docstring in code — needs a one-line description)_

### `User.getData(name: str, user: Optional[str] = None, bot_id: Optional[str] = None, api_key: Optional[str] = None) -> Any`

_(no docstring in code — needs a one-line description)_

### `User.getDataFile(name: str, user: Optional[str] = None, output_format: str = 'txt') -> IO[bytes]`

_(no docstring in code — needs a one-line description)_

### `User.saveData(name: str, data: Any, user: Optional[str] = None, data_type: Optional[str] = None, bot_id: Optional[str] = None, api_key: Optional[str] = None) -> dict`

_(no docstring in code — needs a one-line description)_
