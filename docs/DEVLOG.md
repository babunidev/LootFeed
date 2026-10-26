# LootFeed — the creative and technical development journal

**Original creator and creative director:** Babuni.dev  
**Retrospective prepared:** October 26, 2026. Evidence: current README, TOC, both Lua source files, PowerShell migration utility and test, screenshots, dated Git diffs and v0.2.2 release metadata. This is **not a transcript of every earlier conversation**; unknown motives are labeled rather than invented.

## Beginning with a small reward moment

The idea embodied in LootFeed is that picking up an item should be a pleasant, legible moment rather than another easy-to-miss chat message. The game's inventory system already knows what a player owns; this addon was designed to show the **event of receiving** something, briefly and clearly, while the player continues adventuring.

Its initial identity was **Octo Loot**. The first substantive Git commit, **`dde82cb8130f`** on September 8, 2026, was already described as a **0.1.5 release**. It came with one Lua source file, a 1.12 TOC, a concise README and three screenshots. That is the earliest preserved committed state, not proof that the entire idea and all of its design trials were completed in one sitting. Earlier creator dialogue or local test sessions were not recovered in the repository review.

The interface at that point was intentionally compact. There were **five small rows**, each able to display an item texture, colored name and stack count; money had a coin icon, and genuine Quest Items could receive a gold secondary label. These rows appeared with a modest scale/pop and moved into a stack before fading after a short duration. Hovering both paused the messages and surfaced the native item tooltip.

The original design offered player-controlled location, scale, lifetime and whether the stack builds up or down. A tiny green drag handle became visible only when unlocked. The appeal is in *reducing friction*: a player can keep playing, and each useful pickup remains momentarily intelligible.

Several small technical details express that direction. An item icon may not be cached when the item chat event arrives, so the UI retries `GetItemInfo` later rather than permanently displaying a question mark. Identical item links are combined **while waiting in the queue**, but an already displayed row retains its own short history. A Quest Item is detected by reading its tooltip's binding text, not by guessing whether a material is used by some quest.

Even the money icon tells a story about working within old client resources. Source comments say the developer visually inspected the Vanilla interface MPQ coin icons and selected different item textures for copper, silver and gold. This is historical technical evidence, not an independent repeat of that inspection in October.

## September 11 — a quick personal feed becomes group-aware

A few days later, commit **`fbbd50fac74d`**, “Add persistent group-roll entries with live dice tooltips,” expanded the addon from brief *personal loot* into a window on a **shared decision**.

A group Need/Greed/Pass roll cannot be represented well by one fading item notification. The roll remains in progress while several players make choices, sometimes while numerical results arrive later. A new **Rolls.lua** module appeared, handling roll start/cancel events, item-link keyed chat messages, participants, choices, numbers, winners and the possibility that results simply never appear.

The UI gained **additional roll rows** beyond its five ordinary rows. Each had a die drawn directly through Lua, with a small tooltip enumerating the players. The design left Blizzard's actual rolling buttons intact. LootFeed watched and explained a collective activity; it did not decide Need or Greed on the player's behalf.

This feature introduced an important technical dependency of a different kind: the addon needs detailed loot-roll chat. Its `Rolls.Init` enables the game CVar `showLootSpam` when disabled. There is no bundled library requirement, but there **is** a user-setting side effect. The current source does not save/restore the previous setting. This is a review item the portfolio should preserve rather than assuming it was consciously approved in all circumstances.

The source parses the game's own `LOOT_ROLL_*` localized templates, including numbered positional placeholders. Yet those messages still identify a player and item link without a stable ID for **which instance of an identical item** is being rolled. When two identical drops are up simultaneously, the addon declines to invent assignments and marks the outcome ambiguous. Unknown participant choices remain “Waiting / eligibility unknown.”

The developer created a **/oloot testroll** preview (now **/lfeed testroll**) that simulates ExampleMage and ExampleRogue choices. It is clearly labeled as a preview, offering a lightweight way to inspect the die/tooltips without asking a group of real players to run a test.

## Later September 11 — a bug in what “finished” meant

The second September 11 commit, **`86036564974e`**, is an especially revealing piece of development history. Its message was “Keep tracking group rolls after the local prompt closes.”

The initial roll handling had treated closing the *local* UI prompt as evidence of approaching group completion, with a short cancellation grace period. That assumption was too broad. The game's prompt can close for one player while choices and results are still traveling through the group.

The new code records `promptClosed` without making the roll done. It waits for **a genuine winner message, everyone passing, or the original deadline**. If the deadline arrives without proof, the record enters **Ended (result unavailable)** rather than assigning a fictional winner.

Even that status isn't necessarily the final word. A late authentic result can correct an earlier provisional timeout, while delayed numeric rolls may still arrive after a winner. The source retains completed records for a bounded time and refreshes the visual row when the status changes.

This is a concrete correction of an earlier implementation hypothesis. The commit demonstrates the code change; it doesn't reveal an entire debugging dialogue, a particular user's complaint, or a successful in-game reenactment. Keeping the distinction protects the project's true history while still explaining why the present solution exists.

## September 18 — changing names without losing the player's settings

Commit **`6126df1f90d7`**, “Use provider-neutral names and client compatibility descriptions,” renamed **Octo Loot** to **Loot Feed**, the project to `LootFeed`, the Lua globals, frame/TOC identifiers and account-wide saved variable from **OctoLootDB** to **LootFeedDB**. The manifest advanced to **v0.2.2**.

The rename made the tool's function easier to recognize independently of a named server provider. But even a simple notification addon can store valued personal settings: where the feed lives, how large it is, how long messages last, stack direction and chat mute status. If the new TOC points to a different SavedVariables root, the game won't automatically load the previous file.

So the repository gained **`Upgrade-SavedData.ps1`** and **`tests/test_upgrade.ps1`**. The helper searches the existing saved-data folders, reads old/new TOC declarations, copies and backs up originals, and rewrites only top-level assignments without evaluating saved Lua. It refuses to overwrite an existing destination. The fixture exercises synthetic values, Unicode, backups and path/overwrite refusals. These safeguards are present in code; this October audit did not run them against any files.

A public [v0.2.2 release](https://github.com/babunidev/LootFeed/releases/tag/v0.2.2) was published September 18 with an installation ZIP and SHA256SUMS file. Its release notes report packaging/syntax and migration testing, while continuing to recommend actual in-game verification. Those are **historically reported results**, not fresh test passes.

## October 26 — making the design process part of the artifact

The next visible work in Git took place October 26, when commits updated the TOC and README credit to **Babuni.dev** and created a small documentation scaffold. The current documentation session expands that into a full user guide, technical architecture, design/asset inventory, decisions record and this chronological journal.

The audit caught three easy-to-miss distinctions worth making permanent. The “five rows” limit applies to **ordinary loot**, not all roll rows; completed roll details are **transient**, not a stored player-history database; and `CANCEL_LOOT_ROLL` doesn't prove a group has finished. It also noted the global `ChatFrame_OnEvent` wrapper and `showLootSpam` side effects that should be tested alongside other addons.

The README's download link originally used an older account name. Checking live GitHub release metadata allowed the documentation to point at the current public **babunidev v0.2.2 ZIP** without pretending its bytes were inspected. The three existing screenshots were preserved and referenced as evidence of how the interface looks in the repository.

**Human creator's contribution:** the original loot experience and product direction, with code and subsequent revisions under Babuni.dev's authority. **AI contribution in this October documentation pass:** source and Git inspection, explanation of behavior/tradeoffs, narrative reconstruction and Markdown commits. The Git data does not attribute every 2026 Lua line to any single human or AI participant, and the journal does not invent those claims.

**Verified:** repository contents, dated commits and release metadata, followed by documentation/file-link checks. **Not performed:** running WoW, testing roll events or chat filter with other addons, executing PowerShell, downloading/checksumming the ZIP, moving the user's UI, or touching SavedVariables.

## The next questions belong in play

When many loot items and group rolls arrive together, does the five-row personal cap preserve the right balance? How often is another addon changing the chat handler? Does the user welcome the automatically enabled detailed loot spam, or should that be a choice? How does the little die tooltip feel at different UI scales?

The resulting portfolio entry can show more than an animated loot list. It can show how a quick celebration of receiving something grew into **a careful account of uncertain shared decisions**, and how compatibility and data preservation required subsequent iteration.
