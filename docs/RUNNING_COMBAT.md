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

You can also click **+ Add** to add more combatants. A modal offers three options:
- **Ad-hoc** - name and initiative for lair actions, environmental effects, or custom entries
- **Player** - name, initiative, AC, and notes for a player joining mid-combat (e.g., split party reconvening)
- **Monster Template** - search your monster library, set a quantity, and add full stat-block monsters with auto-rolled initiative

When all players have initiative values, click **Begin Combat** to sort everyone by initiative (descending) and start Round 1.

## The Combat Screen

### Top Bar

- **Round N** badge showing the current round
- **<< Prev** to go back one turn
- **Next >>** to advance to the next turn (this is the button you will click most)
- **+ Add** to add a combatant mid-combat
- **End Combat** to end and remove the combat

### The Initiative List

Each combatant is a row in the list. The current turn is highlighted.

A collapsed row shows:
- **Init** value
- **Name** with inline badges for active conditions, concentration, and legendary action/resistance counts
- **AC** (monsters from template, players editable in combat)
- **HP bar** (monsters only) - color-coded green/yellow/red based on remaining HP, showing current(+temp)/max
- **R** (reaction indicator) - green when available, red when used. Click to toggle.

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
