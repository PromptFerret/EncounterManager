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
- **<< Prev** to go back one turn
- **Next >>** to advance to the next turn (this is the button you will click most)
- **+ Add** to add a combatant mid-combat (see [Adding Combatants](#adding-combatants) below)
- **End Combat** to end and remove the combat

### The Initiative List

Each combatant is a row in the list. The current turn is highlighted.

A collapsed row shows:
- **Init** value
- **Name** with inline badges for active conditions, concentration, and legendary action/resistance counts
- **AC** (monsters from template, players editable in combat)
- **HP bar** (monsters only) - color-coded green/yellow/red based on remaining HP, showing current(+temp)/max
- **R** (reaction indicator) - green circle when available, red when used. Click to toggle. Automatically resets to green at the start of the combatant's turn.

Click any row to expand its detail panel.

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

**Attacks**

For each attack defined on the template:
- Attack name, hit bonus, and note
- Five buttons:
  - **Atk** - rolls 1d20 + hit bonus (normal)
  - **Adv** (green) - rolls with advantage (two d20s, takes higher)
  - **Dis** (red) - rolls with disadvantage (two d20s, takes lower)
  - **Dmg** - rolls all damage dice for this attack
  - **Crit** (green) - rolls critical damage (doubles the dice count, not the modifier)
- Damage summary showing dice + type for each damage entry

All roll results appear in the **roll log** below the attacks. Crits show as "NAT 20!" and fumbles show as "NAT 1".

**Roll Log**

A scrolling list of all rolls made for this combatant during the current turn - attack rolls, damage, ability checks, saving throws, feature dice, and recharge rolls. The log clears at the start of the combatant's next turn.

If the combatant's panel is collapsed and there are unread roll entries, a pulsing notification badge appears on their row.

### Right Column

**Ability Scores**

Shows all six scores with modifiers (e.g., "STR 16 (+3)"). Each has two buttons:
- **Chk** - rolls an ability check (1d20 + modifier)
- **Save** - rolls a saving throw (1d20 + save bonus if proficient, otherwise modifier)

**Speed and Senses** - shown if defined on the template.

**Features** - each feature from the template with its description. Dice notation in the text (like `2d6+3`) is clickable and rolls to the log. Features with recharge and use tracking are covered in [Combat Deep Dive](COMBAT_DEEP_DIVE.md).

**Tactics** - the DM notes from the template, if any.

## Expanded Detail Panel (Players)

Players show an editable **AC** field and a note that conditions and details are tracked by the DM. Players manage their own HP and abilities.

## Expanded Detail Panel (Ad-hoc)

Ad-hoc combatants (lair actions, environmental effects) show a label identifying them as custom entries.

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

## The Combat Loop

A typical turn looks like this:

1. **Next >>** to advance to the next combatant
2. Expand their panel (if not already)
3. Use the multiattack reminder to decide which attacks to make
4. Click **Atk** (or **Adv** / **Dis**) for each attack roll
5. Check the roll log for hits and crits
6. Click **Dmg** (or **Crit**) for attacks that hit
7. Apply damage to targets by expanding their panel and using **Dmg**
8. Handle any conditions, reactions, or legendary actions as needed
9. **Next >>** to move on

The roll log clears when you advance past a combatant's turn, so review results before moving on.
