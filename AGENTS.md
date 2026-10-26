# LootFeed — project collaboration and development rules

**Creative direction / decisions:** Babuni.dev. **Installed identity:** LootFeed v0.2.2; historic name **Octo Loot**.

## Preserve the authored UX

- Vanilla 1.12 / Lua 5.0 conventions; TOC **11200**, account-wide **LootFeedDB**.
- Compact **five ordinary notification rows** with gentle pop, slide, fade, icons, rarity colors and stack quantities; do not convert the feed into a raid damage meter or traditional loot-management window.
- **Group-roll entries are extra rows**, remain while unresolved and briefly after completion, with a drawn die and tooltip; use the game's normal Need/Greed/Pass UI.
- Distinguish **personal item loot** from money and other players' loot. The `chat off` option suppresses only recognized personal item loot in original chat.
- Classify Quest Item through **tooltip `ITEM_BIND_QUEST` text**, not all materials used in quest recipes.
- Do not invent a winner, eligibility, unique roll identity or participant choice when Vanilla does not provide it. Same-link simultaneous rolls are ambiguous; an expired roll is “result unavailable,” and later authentic results can correct provisional status.
- **`CANCEL_LOOT_ROLL` is local prompt closure, not necessarily group-roll completion.** Preserve the Sep 11 v0.2.1 correction.
- `showLootSpam` is changed to `1` if it was `0`; this is a gameplay-setting side effect. Any modification needs creator consent and tests.
- A global `ChatFrame_OnEvent` wrapper can interact with other chat addons. Avoid silent global refactoring.
- Migrate old **OctoLootDB** safely to **LootFeedDB** when renaming. Never overwrite someone's only saved data.
- The repo has **no LICENSE**; do not invent rights for the code, screenshots or upstream game assets.

## Documentation and test discipline

Before editing read both Lua files, TOC, README, concept, roadmap, architecture, decisions, DEVLOG, migration script and PowerShell fixture. Verify actual current tree, not remembered branch state.

Test regular item queues separately from roll lifecycles; test duplicate links, delayed results, global chat wrapper composition, UI scales, timing and localization on the **actual client**. Synthetic test scripts are not live gameplay tests. The current repository contains a saved-data migration fixture, **not** a comprehensive loot/roll parser harness.

For substantial work, preserve:
1. Creator's goal and exact constraints.
2. AI proposal and alternatives (label suggestions as unapproved until chosen).
3. Iteration including misunderstandings, rejected approaches and bug discoveries.
4. Explicit acceptance/rejection/postponement from Babuni.dev.
5. Implementation files, commit, migration implications.
6. Verification actually run, results and remaining unknowns.

Maintain README, CONCEPT, ROADMAP, CHANGELOG, AGENTS, and `docs/DEVLOG.md`, `DECISIONS.md`, `DESIGN.md`, `ARCHITECTURE.md`, `CONTENT.md`, `AI_COLLABORATION.md`. Do not publish private players' loot history, chat or credentials. The 2026-10-26 job is documentation-only.
