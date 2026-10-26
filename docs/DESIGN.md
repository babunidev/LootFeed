# LootFeed — interface and interaction design

**Creator and creative director:** Babuni.dev.

## A small celebration that doesn't become a second inventory

The feed is meant to be **noticed briefly**, not always visible. Standard entries are dark, compact rows that show a recognisable item icon, rarity-colored title and stack count. Coin drops use simple denominations; real Quest Items get a discreet golden label. Items pass through a smooth pop, slide and fade rather than suddenly appearing as chat-line text.

The initial September 8 version deliberately allowed only **five ordinary rows**. This constraint preserves screen space and keeps a busy looting moment from turning into a permanent inventory summary.

![Standard loot feed](../Screenshots/loot-feed.png)

## Positioning and timing

The green anchor handle becomes interactable with **/lfeed unlock**. The player drags it to a preferred side of the interface and locks it afterward. Scale ranges **0.6–1.8**; duration ranges **2–20 seconds**; the stack can build **upward or downward**. These settings are account-wide, not per-character.

Hovering an item reveals the game's tooltip while pausing the entire visual stack. That turns a fast notification into inspectable information without a separate details panel. An item whose client cache is still loading falls back to textual/icon placeholders and refreshes when more data arrives.

![Hover item tooltip](../Screenshots/item-tooltip.png)

The gold **Quest Item** label relies on the actual game's tooltip binding text, not on guessing which items a current quest needs.

![Quest item indicator](../Screenshots/quest-item.png)

## Roll details should remain inspectable

Group rolls don't fit in the five-row constraint. They can last longer than a standard loot notification and need room for people and numbers to arrive in stages. The design therefore creates **additional roll rows** that remain while unresolved, show a drawn five-pip die, and keep the winner near that die when known.

Hovering over the dice opens a compact detailed list of Need/Greed/Pass choices and numeric rolls, including waiting/unknown states. The built-in game Need/Greed/Pass dialog remains the place where the player makes a choice; LootFeed is observational only.

Two important UI messages resist false precision:
- Simultaneous identical item drops can be **ambiguous** because item-link chat messages don't identify the unique roll.
- A deadline without a received winner becomes **result unavailable**, which may later update if a real message arrives.

## Behavioral surprises requiring usability review

The source automatically turns on the game's detailed **showLootSpam** option when it was off so the roll details can be received. That has an effect beyond LootFeed's own tiny interface. The optional **/lfeed chat off** also wraps a global chat handler to hide personal loot lines, which may affect interaction with other addons using the same handler.

These are implemented choices, not silently approved future changes. Decide whether a clear opt-in/explanation for the game CVar would better support the creator's direction after an actual in-game test.

## Accessibility tests still outstanding

Test small-scale text contrast, accessible alternatives to color-only rarity distinctions, animation sensitivity, hover-to-pause discoverability, 24px dice-hit targets and busy simultaneous roll displays. No such test was run in this documentation session and no redesigned interface was implemented.
