[EncounterManager](https://promptferret.github.io/EncounterManager/) | [Docs](INDEX.md) | Parties & Encounters

# Parties & Encounters

Before you can run combat, you need a party (your players) and an encounter (the monsters they will face).

## Parties

A party is a named group of player names. That's it - no stats, no classes, no levels. EncounterManager is a DM tool, and players manage their own characters.

### Creating a Party

1. Go to the **Parties** tab
2. Click **+ New Party**
3. Enter a **Party Name** (e.g., "Tuesday Night Crew")
4. Type a player name and click **Add** (or press Enter)
5. Repeat for each player
6. Click **Save**

Players appear as chips below the input. Click the **x** on a chip to remove a player.

### Why No Player Stats?

EncounterManager tracks the DM's side of the screen. Your players track their own HP, spell slots, and abilities. The only player info EncounterManager needs is a name, an initiative roll (entered when combat starts), and optionally their AC (editable during combat). Everything else stays with the player.

## Encounters

An encounter is a named group of monsters pulled from your templates.

### Creating an Encounter

1. Go to the **Encounters** tab
2. Click **+ New Encounter**
3. Fill in the details:
   - **Name** (required) - e.g., "Ambush at Cragmaw"
   - **Campaign** - optional, for your own organization
   - **Location** - optional, shown when starting combat
   - **Notes** - optional, any prep notes
4. Use the **monster picker** to add monsters (details below)
5. Click **Save**

### The Monster Picker

The encounter form has a searchable picker for adding monsters:

1. Start typing a monster name in the search field
2. A dropdown shows matching monsters from your templates, each with a CR tag
3. Click a monster to select it
4. Set the **quantity** (1-20)
5. Click **Add**

If you add the same monster again, its quantity increases instead of creating a duplicate entry.

Each added monster shows in a list below the picker with an editable quantity and a remove button.

### Editing and Deleting

From the encounter list:
- Click **Edit** to reopen the form and change monsters, quantities, or details
- Click **Delete** to remove the encounter

### Starting Combat from an Encounter

Each encounter card has a **Combat** button. Click it to start combat with that encounter. See [Running Combat](RUNNING_COMBAT.md) for what happens next.
