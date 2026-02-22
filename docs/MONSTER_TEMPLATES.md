[EncounterManager](https://promptferret.github.io/EncounterManager/) | [Docs](INDEX.md) | Monster Templates

# Monster Templates

Monster templates are reusable stat blocks. Build them once, then add them to any number of encounters. When combat starts, each monster gets its own copy with rolled HP and initiative.

## Creating a Template

1. Go to the **Monsters** tab
2. Click **+ New Monster** in the toolbar
3. Fill in the stat block (details below)
4. Click **Save**

The template appears in your monster list, searchable by name and tagged with its CR.

## The Stat Block Form

### Basic Info

The top section has two rows of fields:

| Field | What it does |
|-------|-------------|
| **Name** | Monster name (e.g., "Goblin") |
| **Size** | Dropdown: Tiny, Small, Medium, Large, Huge, Gargantuan |
| **Type** | Creature type (e.g., "Humanoid", "Fiend") |
| **Alignment** | Alignment text (e.g., "Chaotic Evil", "Unaligned") |
| **AC** | Armor class number |
| **AC Note** | Source of AC (e.g., "natural armor", "plate") |
| **HP Max** | Maximum hit points |
| **HP Formula** | Hit dice formula (e.g., "2d6", "8d10+24"). The **Avg** button calculates the average and fills HP Max. The **Roll** button rolls the formula and fills HP Max. |
| **Speed** | Speed text (e.g., "30 ft.", "30 ft., fly 60 ft.") |
| **CR** | Challenge rating (e.g., "1/4", "3", "15") |
| **Init Bonus** | Initiative modifier added to d20 rolls |
| **Init Advantage** | Checkbox - if checked, the monster rolls initiative with advantage |

### Ability Scores

Six number fields for STR, DEX, CON, INT, WIS, and CHA. Each label shows the calculated modifier live (e.g., "STR (+3)"). Changing WIS auto-updates the passive perception display.

### Saving Throws

A dynamic list. Click **+ Add Save** to add a row:
- **Ability** dropdown (Strength through Charisma)
- **Bonus** number (the total save modifier, e.g., +5)

Add one row per proficient saving throw.

### Skills

A dynamic list. Click **+ Add Skill** to add a row:
- **Skill** dropdown (all 18 D&D skills)
- **Bonus** number (the total skill modifier)

### Defenses & Senses

| Field | Example |
|-------|---------|
| **Damage Resistances** | "fire, cold" |
| **Damage Immunities** | "poison" |
| **Damage Vulnerabilities** | "radiant" |
| **Condition Immunities** | "frightened" |
| **Senses** | "darkvision 60 ft." |
| **Languages** | "Common, Goblin" |

**Passive Perception** has its own field below these. The label shows the auto-calculated value (10 + Perception skill bonus, or 10 + WIS modifier if no Perception skill is set). Enter a number to override the auto value, or leave it blank to use the auto calculation.

### Attacks

**Multiattack** is a text field at the top (e.g., "Two claw attacks"). This text is displayed in combat as a reminder.

**Crits On** sets the minimum d20 roll for a critical hit. Default is 20. Set it lower for homebrew monsters with expanded crit ranges.

Click **+ Add Attack** to add an attack. Each attack has:
- **Name** (e.g., "Scimitar")
- **Hit** bonus (e.g., +4)
- **Note** (e.g., "reach 10 ft.", "ranged 80/320 ft.")

Each attack has one or more **damage entries**. Click **+ Damage** inside the attack to add more. Each damage entry has:
- **Damage** dice (e.g., "1d8+2")
- **Type** (e.g., "slashing", "fire")
- **Note** (e.g., "on hit", "if target is prone")

Below the damage entries, an optional **Description** textarea holds the full attack text - special effects, conditions, saving throws, or any additional details. Dice notation in the description (like `2d6`) becomes clickable in combat. When importing from 5etools, this field is auto-populated with the full attack entry text.

### Features & Abilities

Click **+ Add Feature** to add a trait or ability. Each feature has:
- **Name** (e.g., "Pack Tactics", "Fire Breath")
- **Recharge** (e.g., "5-6" for Recharge 5-6 on a d6 roll). Leave blank for always-available features.
- **Uses** (e.g., 3 for three uses per encounter). Leave blank for unlimited uses.
- **Description** - the full text of the feature. Any dice notation in the text (like `2d6+3`) becomes a clickable roll button during combat.

### Legendary

**Actions/Round** sets the legendary action budget (how many legendary actions the creature can take per round, typically 3).

**Resistances** sets the number of legendary resistances.

When Actions/Round is greater than 0, you can add legendary actions with **+ Add Legendary Action**:
- **Name** (e.g., "Detect", "Tail Attack")
- **Cost** (how many legendary actions it costs, typically 1 or 2)
- **Description** - full text, supports clickable dice notation

### Lair Actions

A dynamic list for creatures that fight in their lair. Click **+ Add Lair Action** to add a row:
- **Name** (e.g., "Tremor", "Volcanic Gas")
- **Description** - the full lair action text. Dice notation becomes clickable in combat.

Lair actions are reference-only during combat - they appear in the detail panel but have no budget or use tracking (unlike legendary actions). When combat starts with a monster that has lair actions, a notification modal lists all available lair actions so the DM knows what's in play.

### Tactics & Notes

A free-text area for DM notes about how the monster fights - target priority, positioning, retreat conditions, or anything else you want to remember during combat.

### Descriptions

Two optional text fields for reference during combat:

- **Player Description** - what the creature looks like, sounds like, smells like. Flavor text the DM can read aloud or reference when a player asks about the creature.
- **DM Description** - lore, behavior patterns, SRD reference text, or background info. Private to the DM.

Both descriptions (along with Tactics) are accessible during combat via a collapsible "Tactics & Descriptions" accordion at the bottom of the monster's detail panel. Each entry is displayed as a card box with a **Copy** button for easy clipboard access. The accordion stays collapsed by default and only appears if at least one of the three fields has content.

## Editing and Deleting

From the monster list:
- Click **Edit** on any monster card to reopen the form
- Click **Delete** to remove a template (this does not affect monsters already in active combats)

## Tips

- Use the **Avg** button on HP Formula to quickly set a standard HP value, or **Roll** for variety
- Put all the details in feature descriptions - they show up in combat with clickable dice
- The Multiattack text is just a reminder. The DM decides which attacks to roll and how many.
- Monster templates are independent of encounters. Edit a template and future encounters using it will pick up the changes.
