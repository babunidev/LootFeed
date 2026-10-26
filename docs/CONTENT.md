# LootFeed — visible content, resource icons and stored preferences

**Creative owner:** Babuni.dev.  
**Scope:** Data and assets displayed by the addon; not a recovered player inventory.

## Visual message types

| UI content | Source data | How it is represented |
| --- | --- | --- |
| Personal item drop | Recognized `CHAT_MSG_LOOT` self-item formats | Item link, item name, cached texture, rarity tint, amount |
| Money | `CHAT_MSG_MONEY` and localized denomination words | Text plus gold/silver/copper icon determined from copper-equivalent amount |
| Quest Item | Actual item binding line of a scanned tooltip | Gold **Quest Item** secondary label |
| Group roll | `START_LOOT_ROLL` + parsed roll chat | Item and dice with players, choices, numbers, status, winner if known |
| Group roll uncertainty | Missing winner or duplicate item-link messages | **Ended (result unavailable)** or explicit ambiguity |
| Preview item feed | `/lfeed test` | Synthetic Linen Cloth, Shadowgem, Peacebloom, Night Watch Shortsword and other examples |
| Preview group roll | `/lfeed testroll` | Synthetic ExampleMage / ExampleRogue; tooltip explicitly calls it a preview |

The addon references the game client's **item/icon textures**, not bundled custom artwork. The dice icon is composed in Lua from a square and five little pips, avoiding a dependency on client-specific dice textures. Rarity coloring comes from the item link's color code.

## Saved data and non-saved state

The manifest declares **SavedVariables: LootFeedDB**. Its current settings are:
- `x, y`: anchor offsets from a defined UIParent anchor.
- `scale`: clamped 0.6–1.8, default 1.
- `duration`: seconds, clamped 2–20, default 6.
- `direction`: up/down, default down.
- `mute`: suppress recognized personal item chat messages via wrapped ChatFrame handler, default false.

**Not saved:** past loot feed messages, queue contents, per-roll participant records, winners, temporary hover/demo state. The phrase “persistent group rolls” historically means visible through the group decision, not stored account-wide.

## Localization and origin of values

Normal self-item templates are drawn from global `LOOT_ITEM_*` strings; rolls are matched using `LOOT_ROLL_*` strings with support for numbered positional placeholders. The quest item binding text references `ITEM_BIND_QUEST`; denominations refer to `GOLD`, `SILVER`, `COPPER`. Text-matching limitations still exist, particularly unusual server formats or positional self-loot strings.

No player's actual group identities, inventories or roll numbers were read or copied into this documentation.

## Repository artwork and release distribution

The current Git tree includes three screenshots:
- [Standard item-and-money feed](../Screenshots/loot-feed.png)
- [Item tooltip inspection](../Screenshots/item-tooltip.png)
- [Quest Item marking](../Screenshots/quest-item.png)

A [published v0.2.2 release](https://github.com/babunidev/LootFeed/releases/tag/v0.2.2) supplies ZIP and checksum files. Screenshots and assets belong in portfolio material only with proper creative/right-holder review. The repo has **no LICENSE**; do not silently assign reuse rights or mistake WoW's game-client art for custom original project images.
