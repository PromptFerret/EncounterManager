[EncounterManager](https://promptferret.github.io/EncounterManager/) | [Docs](INDEX.md) | Running Combat

# Running Combat

This guide covers the core combat loop - starting a fight, rolling initiative, managing turns, dealing damage, and rolling attacks. For conditions, concentration, legendary actions, and other advanced features, see [Combat Deep Dive](COMBAT_DEEP_DIVE.md).

## Starting Combat

1. Go to the **Encounters** tab and click **Combat** on an encounter card
2. **Select a party** from the party cards shown. Each card shows the party name and player list.
3. You are now in **initiative setup**

## Initiative Setup

The initiative setup screen lists all combatants - monsters first, then players.

**Monsters** have pre-rolled initiative values with a breakdown shown (e.g., "1d20+3 = 14"). These are editable if you want to change them.

**Players** have blank initiative fields. Enter each player's initiative roll.

You can also click **+ Add** to add more combatants. See [Adding Combatants Mid-Combat](#adding-combatants) below for details.

When all players have initiative values, click **Begin Combat** to sort everyone by initiative (descending) and start Round 1.

## The Combat Screen

### Top Bar

- **Round N** badge showing the current round
- **<< Prev** to go back one turn (disabled at round 1 first combatant - there is no prior turn). Going back reverses turn-start effects - condition countdowns re-increment, and conditions that just expired are restored.
- **Next >>** to advance to the next turn (this is the button you will click most)
- **+ Add** to add a combatant mid-combat (see [Adding Combatants](#adding-combatants) below)
- **Edit Mode** to enter batch edit mode for modifying monster stats (see [Editing Monsters Mid-Combat](#editing-monsters-mid-combat) below)
- **End Combat** to end and remove the combat

### Combat Info

Below the top bar, a summary shows encounter details for quick reference:

- **Party** - the party name
- **Encounter** - the encounter name
- **Campaign** - campaign name (if set on the encounter)
- **Location** - location (if set)
- **Notes** - encounter notes (if set)

Each field has a **Copy** button for quick clipboard access. Only fields with content are shown.

### The Initiative List

Each combatant is a row in the list. The current turn is highlighted.

A collapsed row shows:
- **Init** value
- **Name** with inline badges for active conditions, concentration, and legendary action/resistance counts
- **AC** (monsters from template, players editable in combat)
- **HP bar** (monsters only) - color-coded green/yellow/red based on remaining HP, showing current(+temp)/max
- **R** (reaction indicator) - green circle when available, red when used. Click to toggle. Automatically resets to green at the start of the combatant's turn.

Click any row to expand its detail panel. The active combatant's panel auto-expands when their turn starts and auto-collapses when the turn advances. This state persists across page refreshes.

## Expanded Detail Panel (Monsters)

The panel splits into two columns.

### Left Column

**Hit Points**

Shows current HP / max HP (with temp HP if active). Three buttons:
- **Dmg** (red) - enter a number and click to deal damage. Temp HP absorbs damage first. Drops to 0 = dead.
- **Heal** (green) - enter a number and click to heal. Capped at max HP. Healed above 0 = revived.
- **THP** (yellow) - enter a number to set temp HP. Takes the higher value if temp HP already exists (does not stack).

**Multiattack**

If the template has multiattack text, it shows here as a reminder (e.g., "Two claw attacks").

**Features**

Each feature from the template with its description. Dice notation in the text (like `2d6+3`) is clickable and rolls to the log. Features with recharge and use tracking are covered in [Combat Deep Dive](COMBAT_DEEP_DIVE.md).

**Attacks**

For each attack defined on the template:
- Attack name, hit bonus, and note
- Two buttons that open roll mode popups:
  - **Atk** - click to choose: Disadvantage, Normal, or Advantage. Normal rolls 1d20 + hit bonus. Advantage rolls two d20s and takes the higher. Disadvantage takes the lower.
  - **Dmg** - click to choose: Normal or Crit. Normal rolls all damage dice. Crit doubles the dice count (not the modifier).
- Damage summary showing dice + type for each damage entry

All roll results appear in the **roll log** below the attacks. Crits show as "NAT 20!" and fumbles show as "NAT 1".

**Roll Log**

A scrolling list of all rolls made for this combatant during the current turn - attack rolls, damage, ability checks, saving throws, skill checks, feature dice, and recharge rolls. The log clears at the start of the combatant's next turn.

If the combatant's panel is collapsed and there are unread roll entries, a pulsing notification badge appears on their row.

### Right Column

**Speed and Senses** - shown at the top for quick reference.

**Passives** - Passive Perception (PP), Passive Investigation (PI), and Passive Insight (PIn). Derived from skill bonuses or ability modifiers.

**Checks & Saves**

A 3-column grid showing all six ability scores. Each ability has:
- A **check button** showing the score and modifier (e.g., "18 (+4)"). Click to open the roll mode popup (Disadvantage / Normal / Advantage). Rolls 1d20 + ability modifier.
- A **save button** showing the save bonus (e.g., "+6"). Click to open the roll mode popup. Rolls 1d20 + save bonus (proficient) or ability modifier.

Proficient saves are highlighted in accent color so they stand out.

**Skills**

A 3-column grid showing all 18 D&D skills. Each skill has a button showing the bonus (e.g., "+4"). Click to open the roll mode popup (Disadvantage / Normal / Advantage). Rolls 1d20 + skill bonus (proficient) or the governing ability modifier.

Proficient skills are highlighted in accent color. Non-proficient skills use the ability modifier (e.g., Religion uses INT mod if not proficient).

### Tactics & Descriptions Accordion

At the bottom of the monster's detail panel (below conditions and concentration), a collapsible **Tactics & Descriptions** section appears if any of these fields have content:

- **Tactics** - DM notes about how the monster fights
- **Player Description** - flavor text the DM can read aloud
- **DM Description** - lore and SRD reference text

Each entry is displayed in a card box with a **Copy** button for quick clipboard access (useful for pasting into chat). The accordion is collapsed by default to stay out of the way.

## Expanded Detail Panel (Players)

Players show an editable **AC** field and a note that conditions and details are tracked by the DM. Players manage their own HP and abilities.

## Expanded Detail Panel (Ad-hoc)

Ad-hoc combatants (lair actions, environmental effects) show a label identifying them as custom entries. They have a roll log that displays condition countdown notifications - useful for tracking timed environmental effects (e.g., a lair action that triggers every 3 rounds).

## Common Controls (All Combatants)

At the bottom of every expanded panel:
- **Init** - editable initiative value (changing it during combat re-sorts and advances the turn)
- **Notes** - free-text DM notes, persisted across refreshes
- **Kill** / **Revive** - toggle dead state (dead combatants are dimmed and struck through)
- **Remove** - permanently remove from combat

## Adding Combatants

Click **+ Add** (available in both initiative setup and active combat) to open the Add Combatant modal. Three toggle buttons at the top select the mode:

### Ad-hoc

For entries with no stats - lair actions, environmental effects, traps, or any custom initiative entry.

- **Name** - what this entry is called in the initiative list
- **Initiative** - defaults to 20 in active combat, blank in initiative setup

Creates a simple entry that tracks initiative, notes, and conditions. No HP, AC, or attacks.

### Player

For a player joining after combat started - split party reconvening, a late arrival, or re-adding a player who was removed.

- **Name** - the player's name
- **Initiative** - their rolled initiative (blank until entered)
- **AC** - armor class (editable in their detail panel later too)
- **Notes** - optional DM notes

Creates a full player entry identical to players added at combat launch. Players added this way are not added to the party definition - the party is a template, combat is the instance.

### Monster

For reinforcements, summoned creatures, or late arrivals with full stat blocks.

- **Monster** - searchable picker from your monster library (type to filter, click to select)
- **Quantity** - how many to add (1-20, default 1)
- **Init Override** - leave blank to auto-roll from the template's initiative bonus, or enter a value to use for all

Each monster gets full HP (from the template's max HP), auto-rolled initiative, and the complete stat block - attacks, features, legendary actions, everything. They are identical to monsters added when launching combat.

**Naming**: Monsters are automatically numbered. If you already have Goblin 1-3 and add 2 more, the new ones become Goblin 4 and Goblin 5. If you have a single "Goblin" (no number) and add another, the existing one is renamed to "Goblin 1" and the new one becomes "Goblin 2".

## Editing Monsters Mid-Combat

Need to adjust a monster's stats during combat? Buffs, debuffs, Dispel Magic, or homebrew adjustments - you can edit any template field on in-flight monsters without changing the original template.

### Single Monster Edit

In any monster's expanded detail panel, click **Edit** (in the common controls at the bottom). This opens the override edit form for that one combatant.

### Batch Edit

For applying the same change to multiple monsters of the same type:

1. Click **Edit Mode** in the combat bar
2. Check the boxes on the monsters you want to edit (only same-template monsters can be selected together)
3. Click **Edit Selected** to open the edit form
4. Make your changes - modified fields are highlighted in yellow and show a revert button
5. Click **Save** to write the changes (stays in the form for further edits), **Close** to exit the form (warns if unsaved changes), or **Cancel** to discard all changes (warns if unsaved)

Overrides are shown on combatant rows with a yellow badge, and individually modified values are highlighted in the detail panel. The original template is never modified.

For the full details on overrides, conflict handling, and what can/cannot be changed, see [Combat Deep Dive](COMBAT_DEEP_DIVE.md).

## The Combat Loop

A typical turn looks like this:

1. **Next >>** to advance to the next combatant
2. Expand their panel (if not already)
3. Use the multiattack reminder to decide which attacks to make
4. Click **Atk** for each attack, then choose Disadvantage, Normal, or Advantage from the popup
5. Check the roll log for hits and crits
6. Click **Dmg** for attacks that hit, then choose Normal or Crit from the popup
7. Apply damage to targets by expanding their panel and using **Dmg**
8. Handle any conditions, reactions, or legendary actions as needed
9. **Next >>** to move on

The roll log clears when you advance past a combatant's turn, so review results before moving on.
