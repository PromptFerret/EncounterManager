[EncounterManager](https://promptferret.github.io/EncounterManager/) | [Docs](INDEX.md) | Combat Deep Dive

# Combat Deep Dive

This guide covers the advanced combat features - conditions, concentration, legendary actions and resistances, feature use tracking, reactions, and running multiple combats. For the basics (initiative, turns, HP, attacks, adding combatants), see [Running Combat](RUNNING_COMBAT.md).

## Conditions

Conditions are tracked per-combatant and visible as colored chips on each row in the initiative list.

### Adding a Condition

At the bottom of any combatant's expanded panel, use the condition form:

1. Select a **condition** from the dropdown: Blinded, Charmed, Deafened, Frightened, Grappled, Incapacitated, Invisible, Paralyzed, Petrified, Poisoned, Prone, Restrained, Stunned, Unconscious, or "Custom..." (which shows an inline text input for the custom name)
2. Select a **duration type**:
   - **Rounds** - lasts a set number of rounds. Enter the round count. Auto-decrements at the start of the combatant's turn and auto-removes when expired.
   - **Save-based** - lasts until a save is made. Select the save ability (STR/DEX/CON/INT/WIS/CHA) and set the DC. A reminder appears in the roll log at the start of the combatant's turn. The DM removes it manually when the save succeeds.
   - **Indefinite** - lasts until manually removed.
3. Optionally enter a **source** (what caused the condition, e.g., "Wizard's Hold Person")
4. Click **Add**

### Condition Display

Conditions show as chips on the combatant's row:
- Round-based: "Stunned (2r)" - the number counts down each turn
- Save-based: "Charmed (WIS DC15)"
- With source: "Poisoned [Basilisk Bite]"

Click the **x** on a condition chip to remove it.

### Automatic Behavior

- Round-based conditions decrement at the start of the affected combatant's turn
- When a round-based condition expires, a message appears in the roll log
- Save-based conditions trigger a reminder in the roll log at the start of the combatant's turn - the DM decides whether to roll the save and remove it

## Concentration

Concentration tracking is available for monsters and players (not ad-hoc combatants).

### Setting Concentration

In the expanded detail panel, type a spell name in the **Concentration** field. A "Conc: [Spell Name]" chip appears on the combatant's row in the initiative list.

### Concentration and Damage

When a concentrating combatant takes damage (via the Dmg button), EncounterManager automatically calculates and displays the CON save DC. The DC is the higher of 10 or half the damage taken.

This is a warning only - the DM decides whether the save succeeds or fails and whether to drop concentration.

### Dropping Concentration

Click the **Drop** button (red, appears only when concentrating) to clear concentration. This is logged in the roll log.

## Legendary Actions

Legendary actions are available for monsters whose templates have an Actions/Round budget greater than 0.

### Display

In the initiative list, monsters with legendary actions show an orange **LA:X/Y** badge (remaining/budget, e.g., "LA:2/3").

### Using Legendary Actions

In the expanded detail panel, the Legendary Actions section shows each action with its name, cost, description, and a **Use** button. Clicking Use deducts the cost from the budget.

The Use button is disabled when the remaining budget is less than the action's cost.

### Restoring Legendary Actions

Legendary actions automatically restore to full at the start of the creature's turn. You can also manually click **Restore LA** (green button in the section header) at any time.

## Legendary Resistances

Legendary resistances are tracked separately from legendary actions.

### Display

Monsters with legendary resistances show an **LR:X/Y** badge in the initiative list.

### Using and Restoring

- **Use LR** - deducts one resistance (disabled at 0)
- **Restore LR** - restores one resistance (disabled when full)

The count display turns red when resistances are at 0.

## Feature Use Tracking

Features with limited uses (set on the template) are tracked during combat.

### Display

In the expanded detail panel, features with uses show a **Uses: X/Y** counter. The counter turns red when uses reach 0.

### Using and Restoring

- **Use** - deducts one use (disabled at 0)
- **Restore** - restores one use (disabled when full)

### Recharge

Features with a recharge range (e.g., "5-6") show "(Recharge 5-6)" in yellow next to the feature name.

When uses are at 0 and a recharge range is set, a **Roll Recharge** button (yellow) appears. Clicking it rolls 1d6. If the result falls within the recharge range, all uses are restored. If not, nothing happens. The result is logged in the roll log either way.

The DM decides when to roll recharge - typically at the start of the creature's turn, following standard D&D rules.

## Reactions

Each combatant row in the initiative list includes a reaction indicator (**R**) on the right side.

- **Green circle** - reaction is available
- **Red circle** - reaction has been used this round

Click the indicator to toggle between available and used. Reactions automatically reset to available (green) at the start of the combatant's turn.

## Multi-Combat

EncounterManager supports running multiple combats simultaneously.

### Starting a Second Combat

Start a new combat from the Encounters tab while one is already running. Both combats exist independently with their own initiative order, HP, conditions, and round count.

### Switching Between Combats

When multiple combats exist, the Combat tab shows a **combat list** with a card for each active combat. Each card shows:
- Combat name (format: "Encounter Name - Party Name")
- Status badge: "Setup" (still in initiative setup) or "Round N" with the current combatant's name
- Alive count (e.g., "5/7 alive")
- Player names

Click a card to resume that combat. From inside an active combat, click **<< List** in the top bar to return to the combat list.

### Use Cases

- **Session breaks** - combat not finished when the session ends? Leave it. Come back next week (or next month) and pick up exactly where you left off. Start other combats in the meantime without losing state.
- **Split party** - two groups fighting in different rooms, each with their own initiative and round count
- **Multiple tables** - running games for different groups? Each combat lives independently with its own state
- **Interruptions** - a running encounter gets interrupted by a second fight elsewhere

## Combat Overrides (Editing Monsters Mid-Combat)

DMs frequently need to modify monster stats during combat - buffs from spells, debuffs from abilities, Dispel Magic removing enhancements, or homebrew adjustments. Combat overrides let you edit any template field on in-flight monsters without permanently altering the original template.

### How Overrides Work

Each monster combatant can have a sparse set of overrides - only the fields you change are stored. The original template stays intact. When combat code reads a monster's stats, it automatically merges the overrides on top of the template. If you remove all overrides, the monster reverts to its original stats.

### Single Edit

Click the **Edit** button in any monster's expanded detail panel (in the common controls area). This opens the override edit form for that one combatant.

### Batch Edit

To apply the same change to multiple monsters of the same type (e.g., all 5 goblins hit by the same buff):

1. Click **Edit Mode** in the combat bar - this disables combat controls (Prev/Next/+Add/End Combat) so combat cannot advance while editing
2. Checkboxes appear on each monster row. Click to select targets.
3. The first selection locks to that monster's template - only same-template monsters can be selected together. Other templates are greyed out.
4. Click **Edit Selected** to open the edit form

### The Edit Form

The form looks similar to the template editor, pre-populated with each combatant's effective values (template + any existing overrides).

- **Modified fields** are highlighted with a colored border and show a **revert button** next to the field. Click the revert button to reset that field back to the template value.
- **Saving throws and skills** support per-item revert. If you modify, add, or delete individual saves/skills, each item gets its own revert button. Deleted items (from the template) appear struck-through with a restore button.
- **Saving throws and skills can be added and removed** (they are referenced by name in combat code).
- **Attacks, features, and legendary actions cannot be added or removed** (they are referenced by array index in combat state - feature uses, attack rolls, etc.). You can edit their values but not change the count.

### Applying Overrides

Click **Apply** to write the changes. For each selected combatant, a minimal delta is computed (only fields that differ from the template). Fields reverted to template values are excluded from the delta.

Click **Cancel** to discard changes. Both Apply and Cancel exit edit mode entirely.

### Conflicting Values in Batch Edit

When batch-editing multiple monsters that already have different overrides for the same field:
- Fields where all selected combatants agree: **editable**, showing the shared value
- Fields where selected combatants disagree: **disabled**, showing "varies". These fields are preserved as-is for each combatant when you Apply.

### Visual Indicators

- **Row badge**: Monsters with active overrides show a visual indicator next to their name in the initiative list, with the name in accent color
- **Detail panel highlighting**: All overridden values are highlighted wherever they appear - AC, abilities, attacks, damage, features, speeds, senses, etc.

### What Overrides Do NOT Affect

Instance state stays on the combatant directly: current HP, temp HP, conditions, concentration, legendary actions/resistances remaining, feature uses, roll log, notes, reaction state, and dead status. These are combat-instance state, not template fields.

### HP Note

Overriding HP max does not auto-adjust current HP. If you increase a monster's max HP, heal it separately. If current HP ends up above the new max, the HP bar shows it and the DM adjusts manually.

## Ending Combat

Click **End Combat** (red) in the top bar. This permanently removes the combat and all its state. Monster templates and encounters are not affected - only the in-progress combat data is deleted.
