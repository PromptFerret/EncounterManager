[EncounterManager](https://promptferret.github.io/EncounterManager/) | [Docs](INDEX.md) | Import & Export

# Import & Export

EncounterManager stores all data in your browser's IndexedDB. This means your data is local, private, and entirely yours - but it also means it can be lost if browser data is cleared, the browser updates, or you switch devices.

**Back up your data regularly.**

## Full Backup

### Saving a Backup

Click **Save Backup** in the toolbar. A file named `encounter-backup-YYYY-MM-DD.squishtext` downloads immediately. This file contains everything: all monster templates, encounters, parties, and active combats.

No dialog, no options - one click, one file.

### Restoring a Backup

Click **Load Backup** in the toolbar. Select a `.squishtext` backup file.

The import uses **smart merge**:
- New items (templates, encounters, parties, combats) are added
- Existing items (matched by internal ID) are skipped, not overwritten
- The status bar reports how many items were added and how many duplicates were skipped

This means restoring a backup will never overwrite your current data. It only adds what's missing.

## Monster Import / Export

### Exporting a Single Monster

On the Monsters tab, each monster card has an **Export** button. Click it to download a `.squishtext` file named after the monster (e.g., `Minotaur.squishtext`).

Share these files with other DMs, keep them as individual backups, or build a personal library outside the browser.

### Importing Monsters

On the Monsters tab, click **Import Monster** in the toolbar. A dialog opens where you can select one or more `.squishtext` files at once.

The same smart merge applies - new monsters are added, duplicates (by ID) are skipped.

## The SquishText Format

All backup and export files use the `.squishtext` format - compressed JSON that is smaller and includes a checksum for integrity. The files include a human-readable header explaining how to decode them.

You can decompress any `.squishtext` file using the [SquishText](https://promptferret.github.io/SquishText/) web tool to get the raw JSON inside.

### What You Can Do With Raw JSON

Once you have the raw JSON, you can:
- **Inspect it** - see exactly what data EncounterManager stores for each monster, encounter, party, or combat
- **Hand-edit it** - change names, stats, abilities, or any other field, then re-compress and import
- **Bulk modify** - write a script to update all monsters at once (e.g., recalculate HP, adjust CR)
- **Feed it to AI** - give the JSON structure to ChatGPT, Claude, or any AI to generate new monsters in the correct format, then compress and import
- **Build tools** - write importers/exporters that integrate with other D&D tools

See the [Monster Format](MONSTER_FORMAT.md) reference for the complete field-by-field schema.

## Storage Details

EncounterManager uses IndexedDB (the `pf_encounter` database). You can inspect your data directly in your browser's DevTools > Application > IndexedDB > `pf_encounter` > `state`.

Storage capacity is typically 50MB or more, which is far more than you will need for monster templates and combat data.

If IndexedDB is unavailable (some privacy modes or older browsers), the app falls back to localStorage silently.

## Tips

- **Back up before major changes** - before deleting monsters, ending combats, or clearing data
- **Back up before browser updates** - browser storage can be cleared by updates or cleanup tools
- **Keep monster exports** - exporting individual monsters creates shareable files that are easy to organize in folders
- **Use AI for bulk creation** - export one monster, decompress with SquishText, give the JSON to an AI with instructions for new monsters, compress the result, and import
