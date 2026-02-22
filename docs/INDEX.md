[EncounterManager](https://promptferret.github.io/EncounterManager/) | Docs

# EncounterManager Documentation

EncounterManager is a D&D combat manager for DMs. Build monster stat blocks, organize parties, construct encounters, and run combat with HP tracking, attack rolling, conditions, concentration, and legendary actions - all in your browser.

**Live app:** [promptferret.github.io/EncounterManager](https://promptferret.github.io/EncounterManager/)

## Quick Start

1. **Create a monster** - Go to the Monsters tab and click "+ New Monster". Fill in the stat block and save.
2. **Create a party** - Go to the Parties tab and click "+ New Party". Name it and add your players.
3. **Build an encounter** - Go to the Encounters tab and click "+ New Encounter". Search for monsters and set quantities.
4. **Run combat** - Click the "Combat" button on an encounter, select a party, enter player initiatives, and begin.
5. **Back up your data** - Click "Save Backup" in the toolbar to download a `.squishtext` file. Do this often - browser storage can be erased without warning.

## Guides

| Guide | What it covers |
|-------|---------------|
| [Monster Templates](MONSTER_TEMPLATES.md) | Creating and editing monster stat blocks - abilities, attacks, features, legendary actions |
| [Parties & Encounters](PARTIES_AND_ENCOUNTERS.md) | Setting up player parties and building encounters from your monster templates |
| [Running Combat](RUNNING_COMBAT.md) | Starting combat, initiative, turns, HP, attacks, dice rolling, ability checks, saving throws, skill checks, the roll log, adding combatants mid-combat |
| [Combat Deep Dive](COMBAT_DEEP_DIVE.md) | Conditions, concentration, legendary actions and resistances, feature tracking, reactions, combat overrides (editing monsters mid-combat), multi-combat |
| [Import & Export](IMPORT_EXPORT.md) | Backups, restoring data, sharing monsters, the SquishText format, importing from 5etools |
| [Monster Format](MONSTER_FORMAT.md) | JSON schema reference for creating or generating monsters with external tools or AI |

## Example Data

Download example files from the [examples folder on GitHub](https://github.com/PromptFerret/EncounterManager/tree/main/examples) and import them to see the tool in action:

- **mythborne-chronicles.squishtext** - Full backup with 3 monsters, a party, an encounter, and an active combat. Use "Load Backup" to import.
- **minotaur.squishtext**, **griffin.squishtext**, **basilisk.squishtext** - Individual monster templates. Use "Import Monster" to import.

## Your Data

All data lives in your browser's IndexedDB. Nothing is sent to a server. We have no accounts, no analytics, and no access to your data.

Browser storage is volatile - clearing browser data, switching browsers, or OS updates can erase it without warning. Use **Save Backup** regularly to keep a copy you control.

Backups are [SquishText](https://promptferret.github.io/SquishText/) compressed JSON. You can decompress them to inspect, hand-edit, or feed to AI to generate new monsters.
