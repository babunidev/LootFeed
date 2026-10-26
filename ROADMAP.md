# LootFeed — verification and development roadmap

**Creator / decision authority:** Babuni.dev.  
**Status:** test plans and open options. They do **not** authorize new code, design or dependency changes.

## Source-backed v0.2.2 baseline

- WoW original 1.12 / Lua 5.0-style APIs; TOC interface **11200**.
- **Five** ordinary loot notifications with queued coalescing, item quality/icon/quantity, cash icon and exact **Quest Item** tooltip detection.
- Separate **roll rows** created as needed, with Need/Greed/Pass, dice-hover details, winner and honest uncertainty states.
- Hover pauses the visible stack and queued insertion; configurable direction, scale **0.6–1.8**, duration **2–20s**, anchor placement.
- Optional personal item-chat suppression through a wrapped `ChatFrame_OnEvent` and a **`showLootSpam` CVar side effect** for roll detail.
- Account-wide `LootFeedDB` with x, y, scale, duration, direction and mute.
- Optional roll preview `/lfeed testroll`; personal item preview `/lfeed test`.
- Octo Loot → Loot Feed migration helper and its **PowerShell fixture test**.
- Published [v0.2.2 ZIP/checksum](https://github.com/babunidev/LootFeed/releases/tag/v0.2.2), notes reporting historic syntax/migration tests; current docs audit didn't execute tests.

## P0 — actually validate the interaction

1. Install v0.2.2 on an intended Vanilla **1.12** client. Verify TOC, two Lua files, slash commands, saved settings and Lua error-free loading.
2. Loot one item, multiple items, money, a stacked item, a real quest-bound item and a **normal item required by a quest**. Verify only genuinely flagged Quest Items display the special label.
3. Send rapid duplicate loot events. Check that **queued identical links** coalesce, while already visible items retain their own row history and five ordinary rows do not expand indefinitely.
4. Test Scale **0.6/1/1.8**, Duration **2/6/20**, direction up/down, locked and unlocked drag, anchors on different UI scales, saved position after reload.
5. Hover an item and its tooltip: visible expiration and insertion should pause, but new loot may still queue. Check item cache retry when `GetItemInfo` initially has no data.
6. Enable `/lfeed chat off`: only self-item loot chat should disappear. Other players' loot, group-roll details and money should continue through intended channels. Test with other addons wrapping ChatFrame_OnEvent.
7. Record the value of `showLootSpam` before installation and after login. **The source changes it to 1 when it was 0 and does not restore it**; decide whether this side effect is acceptable and document actual observed behavior.

## P1 — group roll state and ambiguity

8. Test real Need/Greed/Pass rolls in parties/raids, including numeric roll results, all-pass, winner, interrupted/cancelled local prompt and resolution after the local window closes.
9. Verify the source's **local-prompt-close vs group-resolution distinction**: `CANCEL_LOOT_ROLL` marks `promptClosed` but does not finalize the record.
10. Check a roll with an initially missing item link; the addon polls pending roll IDs for **up to three seconds**.
11. Test two simultaneous rolls of the **same item link**. Tooltip should disclose ambiguity rather than assign numbers/choices to an arbitrary copy.
12. Test a missing result crossing its original roll deadline, then an authentic late result. The status should indicate **result unavailable** until a real result arrives, without inventing a winner.
13. Validate the displayed **15-second** post-resolution lifetime, hover pause, up to 300-second internal completed-record retention, and unlimited extra roll rows relative to five ordinary rows.
14. Test `/lfeed testroll` preview and confirm demo names/roll numbers are visibly marked as a **preview**, not a real event.
15. Test localized `LOOT_ROLL_*` constants including positional specifiers; test `LOOT_ITEM_*` messages separately, since that pattern compiler is simpler.

## P2 — migration, packaging, accessibility

16. Run `tests/test_upgrade.ps1` with PowerShell on temporary data; capture real test output. Next migrate a **backup copy** of actual OctoLoot SavedVariables and inspect results before attempting live user-data migration.
17. Download and checksum-match [LootFeed.zip and SHA256SUMS.txt](https://github.com/babunidev/LootFeed/releases/tag/v0.2.2); inspect internal TOC and files. This audit checked release metadata only.
18. Test row contrast, hover targets, tooltip accessibility, tiny dice pips and animation on smaller/low-FPS screens. Decide whether reduced-motion options would be useful.
19. Add a scoped synthetic Lua regression test for **Rolls.lua** state transitions: duplicate item-link ambiguity, delayed winner, provisional timeout, localization and closed prompt.
20. Decide and document repository licensing; **no LICENSE** exists in this main tree.

## Open creative choices (unapproved)

Does hover pausing the entire feed feel right in intense loot moments? Should the addon ask before changing `showLootSpam`? Should the group roll display offer greater uncertainty cues for missing participant eligibility? Do motion, scale and lifetime need per-category controls? Should the saved-data migration be easier on Linux?

Record actual creator decisions, implementation commits and in-game test results when these questions are answered.
