# EncounterManager

D&D encounter manager and initiative tracker for DMs running combat. Build monster templates, organize parties, construct encounters, and run combat - all in the browser with IndexedDB persistence. No server, no accounts, no dependencies.

**Live:** [promptferret.github.io/EncounterManager](https://promptferret.github.io/EncounterManager/)

## The DM's Combat Dashboard

EncounterManager is built for one person at the table: **the DM**.

Running combat means juggling monster stat blocks, hit points, initiative order, conditions, legendary actions, concentration saves, and feature recharges - all while keeping the game moving. EncounterManager puts all of that in one place so the DM can focus on storytelling instead of bookkeeping.

### What it does

- **Manages everything on the DM's side of the screen** - monster HP, stat blocks, attacks, damage, conditions, initiative order, legendary actions, concentration tracking, feature uses, and recharge rolls
- **Rolls dice** - attack rolls with advantage/disadvantage, damage with crit doubling, inline clickable dice in feature text, ability checks and saving throws
- **Tracks combat state** - who's up, who's next, what conditions expire, what legendary actions are left, what features need recharge rolls
- **Persists everything** - close the tab, refresh the page, come back tomorrow. Your combats, templates, and encounters survive in IndexedDB
- **Runs multiple combats** - switch between simultaneous encounters without losing state

### What it will never do

- **Track player HP, spell slots, or abilities.** Your players manage their own characters. EncounterManager will never ask for or store player stats beyond a name, initiative roll, and AC.
- **Replace the player's character sheet.** This is a DM tool, not a virtual tabletop. Players keep full ownership of their characters.
- **Enforce rules.** The DM decides what happens. EncounterManager rolls dice and tracks numbers - it doesn't tell you a hit missed or a spell can't be cast. CR100 monsters, level 60 players, and homebrew rules are all expected use cases.
- **Phone home.** No server, no analytics, no accounts, no tracking. Your data stays in your browser. Period.

## Your Data, Your Control

All data lives in your browser's IndexedDB - never on a server, never transmitted, never accessible to anyone but you. We don't have accounts, analytics, or telemetry. We have no access to your data. Period.

**Warning:** Browser storage is volatile. Clearing browser data, switching browsers, OS reinstalls, or browser updates can erase IndexedDB without warning. Use **Save Backup** regularly to export your data as a `.squishtext` file you control.

**Power-user tip:** Backups are [SquishText](https://promptferret.github.io/SquishText/) compressed JSON. Decompress them with SquishText to inspect, hand-edit, bulk-modify values, or feed them to AI to generate new monsters for import - no UI required.

## Features

### Monster Templates
- Full stat blocks: abilities, AC, HP (with formula), speed, saves, skills, senses
- Attacks with multiple damage types, multiattack
- Features and traits with recharge and use tracking
- Legendary actions and resistances
- Passive perception (auto-calculated or manual override)
- Customizable crit range for homebrew

### Parties
- Create multiple parties with their own player lists
- Select a party when starting combat

### Encounters
- Build encounters from your monster templates
- Searchable monster picker with CR tags

### Combat
- **Initiative tracking**: monsters pre-rolled with breakdown shown, players entered manually
- **HP management**: damage (THP absorbed first), healing, temp HP - all following D&D rules
- **Attack rolling**: normal/advantage/disadvantage, crit detection, damage rolling with crit doubling
- **Conditions**: 14 standard D&D conditions + custom effects, three duration types (rounds, save-based, indefinite), auto-decrement
- **Concentration**: tracking with automatic CON save DC warnings on damage
- **Legendary actions/resistances**: budget tracking, use/restore buttons, auto-recharge on turn
- **Feature use tracking**: per-feature uses with DM-initiated recharge rolls
- **Inline dice rolling**: clickable dice notation in feature/trait text
- **Multi-combat**: run multiple combats simultaneously, switch between them
- **Roll log**: stacking chronological log per combatant, persisted across refreshes

### Import/Export
- Full data backup/restore in SquishText format (compressed `.squishtext` files)
- Per-monster export/import with multiselect
- Smart merge on import - skips duplicates, keeps existing data

## Example Data

Download [mythborne-chronicles.squishtext](https://github.com/PromptFerret/EncounterManager/blob/main/examples/mythborne-chronicles.squishtext) and import it via **Load Backup** to see the tool in action - includes 3 monster templates, a party, an encounter, and an active combat ready to play.

Individual monsters for **Import Monster**:
- [minotaur.squishtext](https://github.com/PromptFerret/EncounterManager/blob/main/examples/minotaur.squishtext) - CR 3 Large bruiser with Charge, Gore, and Reckless
- [griffin.squishtext](https://github.com/PromptFerret/EncounterManager/blob/main/examples/griffin.squishtext) - CR 2 Large flyer with multiattack and Pounce
- [basilisk.squishtext](https://github.com/PromptFerret/EncounterManager/blob/main/examples/basilisk.squishtext) - CR 3 Medium with Petrifying Gaze and poison bite

## Getting Started

### Use it online

Visit [promptferret.github.io/EncounterManager](https://promptferret.github.io/EncounterManager/) - nothing to install. Data is stored in your browser's IndexedDB.

### Run locally

```bash
git clone https://github.com/PromptFerret/EncounterManager.git
open EncounterManager/index.html
```

Single HTML file, no build step, no dependencies. Save it anywhere and open it in a browser - works directly from `file://`. Falls back to localStorage if IndexedDB is unavailable.

### Host it for your group

Drop `index.html` on any web server or static hosting. Each user gets their own data in their own browser.

## Storage

IndexedDB (`pf_encounter` database, ~50MB+ capacity). On first load, any existing localStorage data is automatically migrated. Falls back to localStorage silently if IndexedDB is unavailable.

Inspect your data: DevTools > Application > IndexedDB > `pf_encounter` > `state`

## Contributing

Single HTML file - no build step, no package manager. Edit `index.html` and refresh.

1. Fork the repo
2. Make your changes to `index.html`
3. Test in a browser (inspect IndexedDB for data verification)
4. Submit a pull request

Read `CLAUDE.md` for detailed architecture docs (code map, state management, combat flow, data schemas, form patterns, event handler safety rules).

See `PLAN.md` for the implementation plan, completed phases, and future roadmap (5etools importer, CritterDB importer, etc.).

## License

[MIT License](LICENSE) - Copyright (c) 2026 PromptFerret

## Links

- [All PromptFerret Tools](https://promptferret.github.io/tools/)
- [PromptFerret](https://promptferret.github.io)
