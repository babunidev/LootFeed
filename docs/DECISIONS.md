# LootFeed — evidence-based decisions, revisions and open choices

**Decision-maker:** Babuni.dev. The record uses committed code, README and Git diffs; it does not manufacture conversations or user acceptance.

## Verified development pivots

| Git evidence | Design/engineering implementation | Limit |
| --- | --- | --- |
| Sep 8 `dde82cb` | Compact animated **five-row** loot overlay with coin/quest/icon information | Initial visible committed implementation; early private prototypes unknown |
| Sep 11 `fbbd50f` | Additional **group roll rows**, visible die and Need/Greed/Pass/number tooltip | Read-only observation; Blizzard's roll UI still owns action |
| Sep 11 `8603656` | Decouple closing a local loot-roll dialog from resolution of the whole group roll | Earlier approach could mark the roll complete too soon |
| Sep 18 `6126df1` | Rename Octo Loot → Loot Feed, saved-data helper, new TOC and release metadata | Migration required for existing account settings |

The code tells us what changed; it cannot prove the full design debate or who typed each implementation line in September.

## Tradeoffs embodied in code

**Ordinary notification limit vs roll expansion:** five regular rows preserve space, but group decisions may need extra simultaneous entries. Both behaviors coexist, rather than truncating important roll status to enforce a universal cap.

**Observed data vs invented certainty:** a chat link identifies an item but not one unique simultaneous roll for identical drops. Ambiguity is displayed explicitly. A timeout lacks a winner and is provisional, so it can be revised by a late authentic message.

**Local prompt vs shared state:** closing the local popup isn't the same as resolving the group. The September 11 correction records this semantic distinction directly in code.

**Chat filtering vs interop:** Vanilla has no modern ChatFrame_AddMessageEventFilter; LootFeed wraps `ChatFrame_OnEvent` to hide only personal item messages. It has a real global-function compatibility surface.

**Data completeness vs control of preferences:** richer roll tooltips require detailed loot spam; the addon sets `showLootSpam=1` if initially off and doesn't restore it. That is a source-visible side effect worth testing and asking the creator about, not something to conceal in a user guide.

**Account-wide preferences, transient events:** display choices carry between characters, while loot and group roll records do not survive reloads. Keeping privacy/lifetime bounded is an observed design outcome, not proof of a specific retention-policy meeting.

**Rename vs continuity:** the general LootFeed name requires migrating old `OctoLootDB`. The helper backs up originals and refuses existing destinations.

## Questions not decided in this session

- Should turning on `showLootSpam` be opt-in and visibly explained at first use?
- Should the game setting be restored on unloading, or is leaving detailed chat enabled desirable?
- Should roll progress remain visible indefinitely if genuine messages are lost, or keep the source's original deadline?
- Should animation/reduced motion or roll/ordinary row duration have separate controls?
- Would a test harness around localized loot-roll parsing be worthwhile?
- How should licensing/redistribution permissions be recorded?

Record explicit Babuni.dev approval/rejection and actual test results when those choices are made. This audit changed documentation only.
