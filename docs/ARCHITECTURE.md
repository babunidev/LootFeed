# LootFeed — architecture and roll-state guarantees

**Source inspected:** `LootFeed.lua` (316 lines), `Rolls.lua` (190 lines), manifest, migration helper and fixture, October 26, 2026. **Static review only**; no game or PowerShell test executed.

## Installed package

```text
LootFeed.toc   Interface 11200, Version 0.2.2
               SavedVariables: LootFeedDB (account-wide)
               Roll s.lua [actual filename Rolls.lua] -> LootFeed.lua
Rolls.lua      LootFeedRolls global; roll records, message parser, timeouts
LootFeed.lua   feed rows, queue, icons, tooltip, animation, chat hook, commands
```

The TOC actually lists **Rolls.lua** first, then **LootFeed.lua**. The game uses the Vanilla-style globals `event`, `arg1` and `this`; no modern loot-history API, external library or server endpoint is required.

## Normal loot and money pipeline

```text
ADDON_LOADED ("LootFeed")
  -> LootFeedDB defaults and scale/duration clamps
  -> compile client LOOT_ITEM_* personal loot patterns
  -> make 5 normal UI row frames
  -> LootFeedRolls.Init(callback)
  -> Place() + lock anchor
  -> wrap original ChatFrame_OnEvent if available

CHAT_MSG_LOOT
  -> also pass into LootFeedRolls.Event for roll parsing
  -> if personal LOOT_ITEM_* match, extract item link / quantity
  -> Enqueue(item)

CHAT_MSG_MONEY
  -> parse localized denominations, select coin icon
  -> Enqueue(money text)

OnUpdate (every frame)
  -> LootFeedRolls.Tick()
  -> inject demos at ~0.7s spacing if active
  -> if hovering, pause visible stack and insertion
  -> expire and animate rows
  -> insert queue at >=0.32s spacing
  -> place/fade current rows
```

`Enqueue` coalesces **identical item links only among queued entries** by summing their quantity; items already active retain their separate display history. The standard `LIMIT=5` covers **non-roll** rows. When full, the oldest normal row is pushed into expiration, preserving the finite on-screen flow. Roll rows are not counted against the ordinary limit; they can create additional frames.

Rows use **270×40** logical dimensions, **5px** gap and a `0.9` compact scale layered onto the user scale. `LootFeedDB.duration` defaults **6s** and is clamped to **2–20s**; scale defaults 1 and is clamped **0.6–1.8**. Entrance pop runs around **0.32s**, fade around **0.7s**, while ordinary insertion is spaced by **0.32s**. Stack position eases toward the target. Up/down direction inverts the offsets.

`GetItemInfo` may be temporarily uncached. The renderer falls back to link text/known icons and retries refresh about every **0.5 seconds** until the texture resolves.

## Item and quest recognition

The source compiles six original `LOOT_ITEM_*` client string constants for self-loot and extracts `|Hitem:...|h` links. This compiler recognizes `%s` and `%d` substitutions but **does not** implement the positional-template mapping present in Rolls.lua, so language/provider support is not uniformly guaranteed.

Quest Item status is determined by scanning an actual `GameTooltip` and matching a line exactly to `ITEM_BIND_QUEST` (or the English fallback). That does **not** mean an ordinary item needed for a quest gets labeled.

Money text is normalized into copper, then picks a coin icon: total >= **10000** → gold, >= **100** → silver, otherwise copper. The note in source says the specific old-client coin icon paths were visually inspected from a client MPQ; this historical code comment does **not** count as new independent validation today.

## Group rolls as stateful records

```text
LootFeedRolls.records[id] = {
  id, link, item key, texture, amount,
  players[name] = { name, choice?, number? },
  order = [participant names],
  status, deadline, done?, winner?, finished?,
  promptClosed?, ambiguous?
}
LootFeedRolls.pending[id] = { duration, untilTime } # if link missing initially
```

The tracker receives `START_LOOT_ROLL`, `CANCEL_LOOT_ROLL` and `CHAT_MSG_LOOT`. On start, it asks `GetLootRollItemLink` and `GetLootRollItemInfo`, then takes an initial participant list from party or raid unit names. A roll with no item link is retried for **three seconds**; once resolved, the deadline is its supplied duration in milliseconds converted to seconds plus **15 seconds**.

`Rolls.Init` compiles client `LOOT_ROLL_*` format strings, including positional `%s`/`%d` placeholders. It also sets `showLootSpam=1` when the value is 0 so choices/numeric rolls are available in chat. The code does not revert that setting later.

For each matched message, the tracker searches for a **single unresolved matching item link**. Vanilla chat does not attach a unique roll ID to every chat choice. If multiple roll records share the link, it marks them ambiguous instead of attaching the message to one arbitrarily.

`CANCEL_LOOT_ROLL` sets **promptClosed** but **does not** declare a result. The group may still be rolling. A “winner” or “everyone passed” message completes the roll; otherwise the original deadline produces **Ended (result unavailable)**, with no invented winner. A genuine later result can update that provisional completion; delayed numeric outcomes may also arrive after a winner. Completed roll records are purged after **300 seconds**.

The UI callback `RollChanged` allocates an extra row if needed, draws a five-pip die, and refreshes a hover tooltip with participant status, numbers, winner or explicit ambiguity. While unresolved, row.age is held near the entrance value; once complete, the roll row displays around **15 seconds**, then fades. Hover pauses the **visible feed**, but `LootFeedRolls.Tick` still processes external roll events before the hover pause.

`/lfeed testroll` creates a **clearly labeled demo roll** with fictional ExampleMage/ExampleRogue members, not a real network action.

## Chat handler integration and risk

Vanilla lacks modern `ChatFrame_AddMessageEventFilter`. `LootFeed.lua` stores the current `ChatFrame_OnEvent` global and replaces it with a delegate that suppresses **recognized personal item CHAT_MSG_LOOT** only when `LootFeedDB.mute` is true. Other chat messages delegate to the prior function. Other addons may also wrap that global; load order, handler replacement and lifecycle need real client compatibility tests. Hiding loot in chat does **not** stop the feed recording it or stop group roll data.

Settings persisted: **x, y, scale, duration, direction, mute** in account-wide `LootFeedDB`. **Roll history is session-memory only.**

## Upgrade helper, release, tests

The 2026-09-18 rename changed **OctoLoot** folder, globals and SavedVariables **OctoLootDB** to LootFeed/LootFeedDB. `Upgrade-SavedData.ps1` reads old/new TOCs, locates matching account/per-character files, backs up old originals, creates new destination files without overwriting, and rewrites only top-level variable declarations without executing Lua. Its test `tests/test_upgrade.ps1` builds synthetic temporary fixtures.

The [v0.2.2 release](https://github.com/babunidev/LootFeed/releases/tag/v0.2.2) lists an installable ZIP and SHA256SUMS and reports historic syntax/migration checks. No loot-parser/roll test suite was found in the current tree, and **this audit did not run any tests**.
