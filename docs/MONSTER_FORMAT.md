[EncounterManager](https://promptferret.github.io/EncounterManager/) | [Docs](INDEX.md) | Monster Format

# Monster Template JSON Format

This document describes the JSON format EncounterManager uses to store monster templates. Use it to create, edit, or generate monsters with external tools, scripts, or AI - then compress with [SquishText](https://promptferret.github.io/SquishText/) and import.

## Getting a Sample

The fastest way to see the format in action:

1. Create a monster in EncounterManager and click **Export** on its card
2. Open the `.squishtext` file in [SquishText](https://promptferret.github.io/SquishText/) to decompress
3. You will see the JSON wrapper and the template object inside

## Export Wrapper

Exported `.squishtext` files contain JSON wrapped in an envelope:

```json
{
  "version": 1,
  "exported": "2026-02-20T12:00:00.000Z",
  "templates": [
    { ... monster template ... }
  ]
}
```

The `templates` array can contain one or more monster templates. When importing, EncounterManager reads this array and adds each template to your library (skipping duplicates by ID).

## Monster Template Object

```json
{
  "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "name": "Minotaur",
  "size": "Large",
  "type": "Monstrosity",
  "alignment": "Chaotic Evil",
  "ac": 14,
  "acNote": "natural armor",
  "hpMax": 76,
  "hpFormula": "9d10+27",
  "speed": "40 ft.",
  "cr": "3",
  "abilities": {
    "str": 18,
    "dex": 11,
    "con": 16,
    "int": 6,
    "wis": 16,
    "cha": 9
  },
  "savingThrows": [],
  "skills": [
    { "name": "Perception", "bonus": 7 }
  ],
  "damageResistances": "",
  "damageImmunities": "",
  "damageVulnerabilities": "",
  "conditionImmunities": "",
  "senses": "darkvision 60 ft.",
  "passivePerception": null,
  "languages": "Abyssal",
  "initBonus": 0,
  "initAdvantage": false,
  "critRange": 20,
  "multiattack": "One gore attack and one greataxe attack",
  "attacks": [
    {
      "name": "Greataxe",
      "bonus": 6,
      "note": "",
      "damages": [
        { "dice": "2d12+4", "type": "slashing", "note": "" }
      ]
    },
    {
      "name": "Gore",
      "bonus": 6,
      "note": "",
      "damages": [
        { "dice": "2d8+4", "type": "piercing", "note": "" }
      ]
    }
  ],
  "features": [
    {
      "name": "Charge",
      "desc": "If the minotaur moves at least 10 feet straight toward a target and then hits it with a gore attack on the same turn, the target takes an extra 2d8 piercing damage.",
      "recharge": null,
      "uses": null,
      "usesMax": null
    },
    {
      "name": "Reckless",
      "desc": "At the start of its turn, the minotaur can gain advantage on all melee weapon attack rolls during that turn, but attack rolls against it have advantage until the start of its next turn.",
      "recharge": null,
      "uses": null,
      "usesMax": null
    }
  ],
  "legendaryActionBudget": 0,
  "legendaryActions": [],
  "legendaryResistances": 0,
  "tactics": "",
  "playerDescription": "",
  "dmDescription": ""
}
```

## Field Reference

### Core Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | yes | Unique identifier (UUID v4). Generate a new one for each monster. |
| `name` | string | yes | Monster name |
| `size` | string | yes | One of: "Tiny", "Small", "Medium", "Large", "Huge", "Gargantuan" |
| `type` | string | no | Creature type (e.g., "Humanoid", "Fiend", "Monstrosity") |
| `alignment` | string | no | Alignment text (e.g., "Chaotic Evil", "Unaligned") |
| `ac` | number | yes | Armor class |
| `acNote` | string | no | AC source (e.g., "natural armor", "plate") |
| `hpMax` | number | yes | Maximum hit points |
| `hpFormula` | string | no | Hit dice formula (e.g., "9d10+27"). Used for rolling HP at combat start. |
| `speed` | string | no | Speed text (e.g., "40 ft.", "30 ft., fly 60 ft.") |
| `cr` | string | no | Challenge rating as text (e.g., "1/4", "3", "15") |

### Ability Scores

| Field | Type | Description |
|-------|------|-------------|
| `abilities.str` | number | Strength score (1-30) |
| `abilities.dex` | number | Dexterity score |
| `abilities.con` | number | Constitution score |
| `abilities.int` | number | Intelligence score |
| `abilities.wis` | number | Wisdom score |
| `abilities.cha` | number | Charisma score |

These are raw scores, not modifiers. The modifier is calculated as `Math.floor((score - 10) / 2)`.

### Combat Fields

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `initBonus` | number | 0 | Initiative modifier added to d20 rolls |
| `initAdvantage` | boolean | false | If true, roll initiative with advantage |
| `critRange` | number | 20 | Minimum d20 roll for a critical hit. Set lower for expanded crit ranges. |
| `multiattack` | string | "" | Multiattack description text (reminder only, not mechanical) |

### Defense and Senses

| Field | Type | Description |
|-------|------|-------------|
| `damageResistances` | string | Comma-separated damage types (e.g., "fire, cold") |
| `damageImmunities` | string | Comma-separated damage types |
| `damageVulnerabilities` | string | Comma-separated damage types |
| `conditionImmunities` | string | Comma-separated conditions (e.g., "frightened, poisoned") |
| `senses` | string | Senses text (e.g., "darkvision 60 ft.") |
| `passivePerception` | number or null | Manual override. `null` = auto-calculate from 10 + WIS mod or Perception skill bonus. |
| `languages` | string | Languages text |

### Saving Throws

Array of objects:

```json
"savingThrows": [
  { "ability": "wis", "bonus": 5 },
  { "ability": "con", "bonus": 6 }
]
```

| Field | Type | Description |
|-------|------|-------------|
| `ability` | string | Ability abbreviation: `"str"`, `"dex"`, `"con"`, `"int"`, `"wis"`, `"cha"` |
| `bonus` | number | Total save modifier (e.g., +5) |

### Skills

Array of objects:

```json
"skills": [
  { "name": "Perception", "bonus": 7 },
  { "name": "Stealth", "bonus": 4 }
]
```

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Skill name (e.g., "Perception", "Athletics", "Stealth") |
| `bonus` | number | Total skill modifier |

### Attacks

Array of attack objects. Each attack can have multiple damage entries (e.g., a bite that does piercing + poison damage):

```json
"attacks": [
  {
    "name": "Bite",
    "bonus": 5,
    "note": "reach 5 ft.",
    "damages": [
      { "dice": "1d8+3", "type": "piercing", "note": "" },
      { "dice": "2d6", "type": "poison", "note": "on hit" }
    ]
  }
]
```

**Attack fields:**

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Attack name (e.g., "Bite", "Scimitar") |
| `bonus` | number | To-hit bonus (e.g., 5 for +5) |
| `note` | string | Range or reach note (e.g., "reach 10 ft.", "ranged 80/320 ft.") |
| `damages` | array | One or more damage entries |

**Damage entry fields:**

| Field | Type | Description |
|-------|------|-------------|
| `dice` | string | Damage dice notation (e.g., "1d8+3", "2d6") |
| `type` | string | Damage type (e.g., "slashing", "fire", "poison") |
| `note` | string | Optional note (e.g., "on hit", "if target is prone") |

### Features

Array of feature/trait objects:

```json
"features": [
  {
    "name": "Fire Breath",
    "desc": "The dragon exhales fire in a 15-foot cone. Each creature in that area must make a DC 11 Dexterity saving throw, taking 7d6 fire damage on a failed save, or half as much damage on a successful one.",
    "recharge": "5-6",
    "uses": 1,
    "usesMax": 1
  }
]
```

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Feature name |
| `desc` | string | Full text. Dice notation like `7d6` becomes clickable in combat. |
| `recharge` | string or null | Recharge range (e.g., "5-6" means recharges on a d6 roll of 5 or 6). `null` for no recharge. |
| `uses` | number or null | Not used by combat - can be omitted or set to `null`. Exists for template-level bookkeeping only. |
| `usesMax` | number or null | **Mechanically important.** Maximum uses per combat. `null` for unlimited. Combat initializes remaining uses from this value and tracks them automatically. |

### Reactions and Bonus Actions

EncounterManager does not have separate fields for reactions or bonus actions. Model them as features with the action type in the name:

```json
"features": [
  {
    "name": "Parry (Reaction)",
    "desc": "The knight adds 2 to its AC against one melee attack that would hit it. To do so, the knight must see the attacker and be wielding a melee weapon.",
    "recharge": null,
    "usesMax": null
  },
  {
    "name": "Healing Word (Bonus Action)",
    "desc": "The cleric casts healing word. One creature within 60 feet regains 1d4+3 hit points.",
    "recharge": null,
    "usesMax": 3
  }
]
```

Include trigger conditions in the `desc` text so the DM knows when the ability applies. The 5etools importer uses this same pattern automatically.

### Legendary Actions

```json
"legendaryActionBudget": 3,
"legendaryActions": [
  {
    "name": "Detect",
    "cost": 1,
    "desc": "The dragon makes a Wisdom (Perception) check."
  },
  {
    "name": "Tail Attack",
    "cost": 2,
    "desc": "The dragon makes a tail attack."
  }
],
"legendaryResistances": 3
```

| Field | Type | Description |
|-------|------|-------------|
| `legendaryActionBudget` | number | Legendary actions per round (typically 3). Set to 0 for no legendary actions. |
| `legendaryActions` | array | Each with `name` (string), `cost` (number), and `desc` (string) |
| `legendaryResistances` | number | Number of legendary resistances. Set to 0 for none. |

### Other Fields

| Field | Type | Description |
|-------|------|-------------|
| `tactics` | string | DM notes about how the monster fights. Shown in combat detail panel under the Tactics & Descriptions accordion. |
| `playerDescription` | string | Flavor text - what players see (appearance, sounds, smells). Accessible during combat via the accordion with a Copy button. |
| `dmDescription` | string | DM-only lore, SRD reference text, behavioral notes. Accessible during combat via the accordion with a Copy button. |

## Creating Monsters for Import

### By Hand

1. Copy the template object above as a starting point
2. Generate a new UUID for the `id` field (any UUID v4 generator works)
3. Fill in your monster's stats
4. Wrap in the export envelope: `{ "version": 1, "exported": "...", "templates": [your_template] }`
5. Compress with [SquishText](https://promptferret.github.io/SquishText/)
6. Save as a `.squishtext` file and import into EncounterManager

### With AI

1. Export an existing monster to get a working example
2. Decompress it with [SquishText](https://promptferret.github.io/SquishText/)
3. Give the JSON to an AI (ChatGPT, Claude, etc.) with instructions like "Create a CR 5 fire elemental in this same format"
4. Make sure the AI generates a new UUID for the `id` field
5. Wrap in the export envelope, compress, and import
6. You can include multiple templates in the `templates` array to bulk-create monsters

### Multiple Monsters

The `templates` array supports multiple monsters in a single file:

```json
{
  "version": 1,
  "exported": "2026-02-20T12:00:00.000Z",
  "templates": [
    { "id": "...", "name": "Goblin", ... },
    { "id": "...", "name": "Hobgoblin", ... },
    { "id": "...", "name": "Bugbear", ... }
  ]
}
```

Import will add all of them at once, skipping any whose `id` already exists in your library.

## UUID Generation

Each template needs a unique `id` in UUID v4 format (e.g., `a1b2c3d4-e5f6-7890-abcd-ef1234567890`). If you are generating monsters with AI or scripts, make sure each one gets a unique ID. Most languages have UUID libraries, or you can use an online generator.

Duplicate IDs are how EncounterManager detects "already imported" monsters. If two different monsters share an ID, the second one will be skipped on import.
