# LootFeed — dated Git history and release evidence

**Project creator:** Babuni.dev. **Dates are commit and release timestamps**, not promises of an in-game playtest.

## 2026-10-26 — full documentation

- Rebuilt 11 Markdown files, preserving existing screenshots, Lua, manifest, PowerShell helper and migration fixture unchanged.
- Documented default five normal rows **versus additional active roll rows**; precise queue coalescing, hover timing and result life.
- Preserved Sep 11 group-roll feature and correction of the assumption that closing a local prompt means group resolution.
- Flagged `showLootSpam` and `ChatFrame_OnEvent` side effects, known ambiguity of duplicate simultaneous items, and client/localization testing gaps.
- Verified the current **v0.2.2 release with ZIP and checksum** exists; corrected the README's old-owner release link to the current `babunidev` repository.
- No scripts/tests/game source changed; no runtime tests executed.

## 2026-09-08 — Octo Loot v0.1.5

**`dde82cb`**, “Release Octo Loot 0.1.5,” established a one-Lua-file Vanilla loot overlay: five ordinary animated rows, icon/quality/quantity, money denomination, tooltip-based quest item identification, a draggable handle, duration/scale/direction, and optional chat hiding. The original installer/TOC called the project **Octo Loot**, storing **OctoLootDB**.

## 2026-09-11 — group-roll tracking (v0.2.0)

**`fbbd50f`**, “Add persistent group-roll entries with live dice tooltips,” added **Rolls.lua**, `START_LOOT_ROLL` and loot-message processing, separate long-lived roll rows, dice rendering and a detailed Need/Greed/Pass tooltip. It also introduced `/oloot testroll` preview and enabled detailed `showLootSpam` reporting when off.

“Persistent” in this commit means **persists on screen across a roll**, not SavedVariables archival roll history.

## 2026-09-11 — resolving the true lifecycle (v0.2.1)

**`8603656`**, “Keep tracking group rolls after the local prompt closes,” fixed an important interpretation of Vanilla's events. Closing the *player's* `CANCEL_LOOT_ROLL` prompt could occur while the **group** was still waiting for results. The change kept the record open until winner/all-pass/deadline, allowed provisional timeout results to be updated by late genuine results, and handled delayed numeric events.

The fix's code and commit are observable; no original chat about the bug has been recovered.

## 2026-09-18 — renamed LootFeed v0.2.2

**`6126df1`**, “Use provider-neutral names and client compatibility descriptions,” renamed `OctoLoot.lua`/`.toc` and the globals/database to **LootFeed**, removed provider-specific labeling, and created a PowerShell saved-data rename tool plus fixture test. New TOC version: **0.2.2**. This change altered the identity used to load old account-wide settings, so migration is part of the functional preservation story.

A [public release v0.2.2](https://github.com/babunidev/LootFeed/releases/tag/v0.2.2) was published September 18 with ZIP and SHA256SUMS. Its notes **report** source/package syntax, migration tests and relevant regression checks passed. Those were **not independently rerun** in the documentation pass.

## 2026-10-26 — credit and first documentation scaffold

Commits `828c6a0`, `afcc482`, `307eeb0` and `7ce832b` standardized Babuni.dev authorship and added/linked a brief initial documentation set. The current pass expands it into a narrative and technical record.
