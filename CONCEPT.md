# LootFeed — creative concept

**Original creator and creative director:** Babuni.dev.  
**Game-client target:** original World of Warcraft 1.12 (TOC 11200).  
**Historical identity:** Octo Loot → Loot Feed.

## Giving rewards a moment on screen

LootFeed is a small interface experiment about **the feeling of getting something**. The ordinary game chat tells a player what was looted, but that information is easy to lose while traveling, fighting and talking. This addon makes those moments briefly visible in a **compact stack of softly animated notifications**. Rarity colors, item art, quantities, money denominations and genuine quest-item labels are placed close together without taking over the game.

The default design has **five short rows for regular loot**, set against a dark translucent backing, with a small entrance pop, smooth vertical movement and a fade-out. The player can move the anchor, choose direction and adjust scale and duration. **Hovering pauses** the stack so an item can be inspected. It is a deliberately temporary visual memory, not an inventory manager or an item database.

The initial September 8 release already carried this visual identity. The exact original brainstorming conversations are not present in the inspected Git history; Babuni.dev retains authorship of the creative direction. This document describes the result rather than inventing an origin scene.

## From receiving loot to understanding group decisions

On September 11 the addon expanded beyond **personal** loot. A group Need/Greed/Pass roll is a shared decision whose details often disappear into the combat or chat stream. LootFeed added **extra, longer-lived roll rows** with a small drawn die. Hovering the die reveals names, choices and roll numbers; once the outcome arrives, the winner is shown directly on the row.

This is an important change in scope. Five short personal notifications are a stream. Roll decisions need **state over time** and sometimes more than five visible rows. Rather than replacing Blizzard's buttons or triggering a roll for the player, LootFeed observes the game's roll events and messages, preserving the player's agency.

## Make uncertainty visible

Vanilla loot chat contains an item link but not a unique roll identifier for every identical concurrent drop. The implementation does not pretend to match a player's response to one indistinguishable copy. It marks those cases as **ambiguous** in the tooltip.

Similarly, a missing winner does not justify inventing one. The row can say **“Ended (result unavailable)”** when the original deadline is exceeded. If a late genuine result arrives, that status is **provisional** and may be updated. When a local roll prompt closes, that is not automatically evidence that the group has completed the roll.

The small interface thus carries a broader creative principle: **give the player useful feedback without claiming knowledge the client does not provide**.

## The project's boundaries

LootFeed doesn't auto-roll, compare another player's fairness, rank loot recipients, redistribute items, or save an archival database of group behavior. It uses old-client Lua UI primitives and optional local chat hiding, with all remaining item/roll decisions made through normal game UI.

Potential improvements—reduced motion, more localization or different roll privacy—remain options for Babuni.dev rather than approved changes. The portfolio should show this as a compact interaction-design work, not expand it into a conventional loot-management dashboard without permission.
