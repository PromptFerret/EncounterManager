# EncounterManager

D&D encounter manager and initiative tracker for DMs running combat. Build monster templates, organize parties, construct encounters, and run combat — all in the browser with IndexedDB persistence. No server, no accounts, no dependencies.

**Live:** [promptferret.github.io/EncounterManager](https://promptferret.github.io/EncounterManager/)

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
- **HP management**: damage (THP absorbed first), healing, temp HP — all following D&D rules
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
- Smart merge on import — skips duplicates, keeps existing data

## Getting Started

### Use it online

Visit [promptferret.github.io/EncounterManager](https://promptferret.github.io/EncounterManager/) — nothing to install. Data is stored in your browser's IndexedDB.

### Run locally

```bash
git clone https://github.com/PromptFerret/EncounterManager.git
open EncounterManager/index.html
```

Works directly from `file://` — falls back to localStorage if IndexedDB is unavailable.

### Host it yourself

Copy `index.html` to any web server or static hosting. Single file, no dependencies.

## Storage

Data is stored in IndexedDB (`pf_encounter` database, ~50MB+ capacity). On first load, any existing localStorage data is automatically migrated. Falls back to localStorage silently if IndexedDB is unavailable.

Inspect your data: DevTools → Application → IndexedDB → `pf_encounter` → `state`

## Design Philosophy

Built for heroic/homebrew D&D — CR100 monsters, level 60 players, non-standard rules are all expected use cases. The tool doesn't enforce official D&D constraints.

## Contributing

Single HTML file — no build step, no package manager. Edit `index.html` and refresh.

1. Fork the repo
2. Make your changes to `index.html`
3. Test in a browser (inspect IndexedDB for data verification)
4. Submit a pull request

Read `CLAUDE.md` for detailed architecture docs (code map, state management, combat flow, data schemas, form patterns, event handler safety rules).

See `PLAN.md` for the implementation plan, completed phases, and future roadmap (5etools importer, CritterDB importer, etc.).

## License

[MIT License](LICENSE) — Copyright (c) 2026 PromptFerret

## Links

- [All PromptFerret Tools](https://promptferret.github.io/tools/)
- [PromptFerret](https://promptferret.github.io)
