# Loot Feed

**Author:** Babuni.dev

A compact, movable loot feed for **World of Warcraft 1.12**. No dependencies.

- Five animated loot rows with item icons, rarity colors, and stack counts.
- Copper, silver, and gold icons, plus a gold **Quest Item** label for quest items.
- Hover to pause the feed and see item tooltips.
- Group rolls stay visible with a dice tooltip showing names, Need/Greed/Pass choices, numbers, and the winner.
- Adjustable position, size, duration, and stack direction.

## Install

Download the published [v0.2.2 LootFeed.zip](https://github.com/babunidev/LootFeed/releases/download/v0.2.2/LootFeed.zip), extract the `LootFeed` folder into `Interface\AddOns`, and restart WoW. Enable **Loot Feed** at character select.

## Upgrade from a previous add-on name

If you used this add-on under a previous folder name, migrate your saved data
before playing with the new installation. A normal file replacement is not
enough when the folder and saved-variable names change.

1. Fully close the game. Keep the previous add-on installed for this step.
2. Install the new `LootFeed` folder beside the previous folder.
3. Open PowerShell in `LootFeed` and run the following, replacing both example values:

   ```powershell
   .\Upgrade-SavedData.ps1 -ClientPath "C:\Games\World of Warcraft" -PreviousAddonName "PreviousAddonFolder"
   ```

The helper reads the previous TOC, copies account-wide and per-character saved
data to the new names, and saves backups under `AddonUpgradeBackups` in the
game folder. It keeps the original files intact and refuses to overwrite
existing saved data for the new add-on. It does not execute saved Lua code.

4. Disable the previous add-on and enable **LootFeed** before entering the world.
5. Check your settings and saved data. Keep the backups until you have verified them.

Further updates using the same add-on name keep your saved data normally.
On other operating systems, back up the files, then copy the previous add-on's
`.lua` file in each `WTF/Account/**/SavedVariables` directory to `LootFeed.lua`.
Change only the top-level variable name declared in the old TOC to the matching
name in the new TOC; leave its table contents unchanged. Do this with the game closed.

## Commands

- `/lfeed unlock` — drag into position; `/lfeed lock` to finish.
- `/lfeed test` — preview the feed.
- `/lfeed testroll` — preview a group roll; hover the dice as it progresses.
- `/lfeed scale 1` — adjust size.
- `/lfeed duration 6` — seconds before fading.
- `/lfeed direction up` or `down` — change stack direction.
- `/lfeed chat off` — hide personal item loot in chat; `chat on` restores it.
- `/lfeed reset` — restore defaults.

Settings are saved account-wide. Quest labels follow the item's tooltip; ordinary materials needed for quests are not marked.

Group rolls use the normal game's Need/Greed/Pass buttons. Active rolls add rows
as needed and stay until resolved; results remain for 15 seconds, paused while
hovering. The winner appears beside the dice. Detailed loot messages
(`showLootSpam`) are enabled to receive everyone's choices and rolls. Players
whose choice has not been received are shown as waiting/eligibility unknown.
If identical items are rolled simultaneously, Vanilla's chat cannot distinguish
the copies; the tooltip reports that limitation instead of guessing. Missing
results time out explicitly without inventing a winner. Rolls in progress before
the add-on loads cannot be reconstructed.

## Screenshots

![Loot feed with item quantities and money](Screenshots/loot-feed.png)

![Quest item label and fading loot](Screenshots/quest-item.png)

![Item tooltip on hover](Screenshots/item-tooltip.png)

## What the source actually does

The installed addon folder is **LootFeed**, with **LootFeed.toc**, **Rolls.lua** and **LootFeed.lua**. The TOC declares interface **11200**, version **0.2.2** and account-wide SavedVariables **LootFeedDB**. The interface registers **/lfeed** and **/lootfeed** as equivalent aliases.

**Ordinary loot:** the five-row limit applies to *normal* item and money notifications. Incoming identical item links can be coalesced while still in the queue, but items already visible are not merged away. Entries animate in, slide to their new stack position, then fade after the selected delay (default **6 seconds**, adjustable from **2–20 seconds**). New rows are inserted no more frequently than about **0.32 seconds**. When ordinary rows are full, the oldest is expired rather than allowing an unlimited stack.

**Group rolls:** roll rows are **extra** and may exceed those five normal rows. They remain displayed while unresolved and can show **Need / Greed / Pass**, reported numbers and eventual winner. On completion, the row remains for around **15 seconds** before fading; hover pauses the visible feed timer. This is session-only roll tracking, **not a permanent saved history of group decisions**. The roll tracker itself retains completed records internally for up to **300 seconds** to handle delayed messages.

The dice are drawn by the addon as a tiny five-pip symbol rather than requiring an external image. Hover the dice for the participant breakdown. The normal Blizzard Need/Greed/Pass window remains in control of the player's actual roll.

**Important interaction side effect:** when the client setting `showLootSpam` is `0`, `Rolls.lua` sets it to `1` so detailed choice and number messages become available. The addon does **not** restore the previous value when switched off/unloaded. Likewise, `/lfeed chat off` wraps the legacy global `ChatFrame_OnEvent` handler to hide recognized **personal item** messages in chat. It does not suppress money messages or group-roll tracking. Review compatibility with other chat-handler addons if both are installed.

**Limits:** the self-loot parser uses the client's `LOOT_ITEM_*` string formats; the roll parser uses `LOOT_ROLL_*` templates with positional placeholder support. Full localized/custom-server behavior is not independently proven. Quest Item detection checks for the binding text on the actual item tooltip; **quest-needed materials** lacking that text are not labeled. A loot item may arrive before `GetItemInfo` finishes caching, so the display retries an item refresh.

## Releases, tests and attribution

The [published **v0.2.2 release**](https://github.com/babunidev/LootFeed/releases/tag/v0.2.2) contains **LootFeed.zip** and **SHA256SUMS.txt**. The September 18 release notes *report* passing packaging/syntax and migration checks; this October documentation review has **not** rerun them or byte-checked the ZIP. The [PowerShell migration fixture](tests/test_upgrade.ps1) uses temporary files; the helper should still be tested on **copies** of real old save files with the game closed.

A bundled test for **loot animation or group-roll parsing** was **not** present in the inspected repository tree. The three [current screenshots](Screenshots/loot-feed.png) illustrate the interface but do not prove every custom client environment behaves the same.

The historical name was **Octo Loot**, with `OctoLootDB`; the September rename changed both addon identity and SavedVariables. Do not simply rename a folder without migrating old settings. The creator is **Babuni.dev**; this documentation pass preserves creative authorship and records later AI research separately.

The current repository tree contains **no LICENSE file**. GitHub visibility alone does not confer a reuse license; licensing is a creator decision.

## Development documentation

- [Creative concept](CONCEPT.md) · [Verification roadmap](ROADMAP.md) · [Dated changelog](CHANGELOG.md)
- [Architecture](docs/ARCHITECTURE.md) · [Interaction design](docs/DESIGN.md) · [Content inventory](docs/CONTENT.md)
- [Design decisions](docs/DECISIONS.md) · [Narrative development journal](docs/DEVLOG.md)
- [Human–AI collaboration](docs/AI_COLLABORATION.md) · [Developer/AI rules](AGENTS.md)

The journal distinguishes source-backed facts, historically reported tests, new verification and unanswered design questions.
