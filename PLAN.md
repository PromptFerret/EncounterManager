# EncounterManager - Implementation Plan

## Overview

D&D encounter manager and initiative tracker for DMs running combat. Single self-contained HTML file at `index.html`. No server, no accounts, no dependencies - IndexedDB persistence (with localStorage fallback).

Built for heroic/homebrew D&D - CR100 monsters, level 60 players, non-standard rules are expected use cases.

## Completed Phases

### Phase 1 - Foundation (done)
- HTML structure: header, nav tabs (Monsters/Parties/Encounters/Combat), status bar, footer
- CSS: full PromptFerret dark theme (`--bg: #0f0f17`, `--surface: #1a1a2e`, `--accent: #7c5cbf`)
- View switching with dirty check warnings
- Monster template CRUD (list with search, form with all fields including abilities, attacks with multiple damage types, features, legendary actions/resistances)
- Encounter CRUD (name, location, campaign, notes, monster picker with qty)
- Party CRUD with per-party player list (type name + Add, remove with × button)
- Dice engine: `rollDice(notation, opts)` - NdN+M, advantage/disadvantage
- Passive Perception auto-calc
- localStorage persistence with `pf_enc_` namespace
- Save/Close/Cancel form pattern with dirty tracking

### Phase 2 - Combat Core (done)
- Combat state machine: empty → party select → init setup (round 0) → active combat (round 1+)
- `launchCombat(partyId)`: creates numbered monster instances, rolls monster init with breakdown text stored as `initRollText`
- Initiative setup: monsters pre-rolled with roll text shown left of input, players blank (they roll in Discord/Avrae)
- Active combat: column headers (Init/Name/AC/HP/R), expandable combatant rows
- Combatant row: init | name (color-coded by type) | AC | HP bar (color-coded) | reaction indicator
- Detail panel (monsters): HP controls, multiattack box, attack cards, roll log, stats, features, tactics
- Detail panel (players): AC input, notes
- Detail panel (all): init edit, notes, kill/revive/remove
- HP tracking: single input + Dmg(red)/Heal(green)/THP(yellow) buttons. THP takes higher (doesn't stack per D&D rules)
- Attack rolling: Atk button per attack opens roll mode popup (Disadvantage/Normal/Advantage). Crit detection on chosen d20
- Damage rolling: Dmg button opens popup (Normal/Crit). Crit doubles dice count not modifiers (NdM → 2NdM)
- Crit highlighting: nat 20 green/bold "NAT 20!", nat 1 red/bold "NAT 1"
- Roll log: stacking chronological log per combatant, persisted to localStorage, survives refreshes, clears on that combatant's next turn start
- Multiattack: prominent yellow-bordered box with "Multiattack:" prefix
- Turn management: next/prev with round tracking, reaction auto-reset on turn start, roll log clear on turn start
- Init drop: editing current combatant's init ends their turn and advances to next
- Ad-hoc combatants: sorted into position when added during active combat
- Player AC: editable in detail panel, shown in row
- Encounter delete clears active combat if linked
- Auto-save on every mutation, resume on page load

## Phase 3 - Combat Features (done)

- Crit range on template schema (`critRange`, default 20, labeled "Crits On" in form, customizable for homebrew 18-20 etc.)
- Full feature/trait text display in combat detail panel with inline clickable dice rolling (`renderDiceText()`)
- Inline dice roll log entries include source feature/LA name (e.g., "Fire Breath: 2d6+3")
- Feature use tracking: `featureUses` sparse map on combatant, Use/Restore buttons (accent/success colored), depleted state display
- DM-initiated recharge: notification in roll log at turn start, Roll Recharge button (warning colored) in detail panel
- Legendary actions: LA budget display on row (badge) and detail panel, per-action Use buttons (cost-aware), Restore LA button for DM take-backs, auto-recharge on turn start
- Legendary resistances: LR counter on row (badge) and detail panel, Use LR + Restore LR buttons
- LA/LR use and restore actions logged to combatant rollLog
- Ability check/save rolling: Chk/Save buttons per ability score in monster detail panel stats section, each opens roll mode popup (Disadvantage/Normal/Advantage)
- Condition/effect tracking: 14 standard D&D conditions + custom text, three duration types (rounds/save-based/indefinite), condition chips on ALL combatant rows, add/remove UI in all detail panels
- Concentration tracking: text input per combatant, chip on row, damage triggers CON save DC warning (`max(10, damage/2)`)
- Consolidated turn-start hook: `applyTurnStartEffects(idx)` handles reaction reset, rollLog clear, recharge prompts, LA recharge, condition decrement/save reminders
- Turn-start notification badges on combatant rows (pulse animation) when panel is collapsed
- Button class system: `.btn-accent`, `.btn-success`, `.btn-warning`, `.btn-danger`, `.btn-remove` - never use inline color styles on buttons (breaks hover contrast)
- Features and legendary actions styled as cards (background + border-radius) matching attack cards
- Roster chips sized for comfortable × button interaction

### Phase 3 Data Changes
New fields on combatant instances (sparse, added on mutation only):
```json
{
  "conditions": [
    { "name": "Frightened", "durationType": "rounds", "duration": 3, "source": "Dragon Fear" },
    { "name": "Stunned", "durationType": "save", "saveDC": 15, "saveAbility": "con", "source": "Power Word Stun" },
    { "name": "Prone", "durationType": "indefinite", "source": "" }
  ],
  "concentration": "Wall of Fire",
  "legendaryActionsRemaining": 3,
  "legendaryResistancesRemaining": 3,
  "featureUses": { "0": 2, "3": 0 }
}
```
New field on monster template:
```json
{
  "critRange": 20
}
```

### Deferred to Backlog
- Surprise round handling
- Dynamic notes/riders with round expiry
- Damage log (collapsible panel)

## Phase 3.5 - IndexedDB Migration + No-Cache (done)

- Migrated from localStorage (~5-10MB) to IndexedDB (~50MB+)
- Auto-migration: on first load, reads localStorage data, writes to IndexedDB, clears localStorage
- Fallback: if IndexedDB unavailable (Safari `file://`), silently falls back to localStorage via `_useLocalStorage` flag
- `save(key)` is fire-and-forget async - callers unchanged, in-memory `state` is source of truth
- `load()` is async - init block changed to `load().then(render)`
- Database: `pf_encounter`, version 1, single object store `state`
- No-cache meta tags added (`Cache-Control`, `Pragma`, `Expires`) so browsers always fetch latest version
- Init value 0 fix: initiative of 0 now displays correctly (not as empty). Null/undefined init is distinct from 0 - empty means "not set", 0 means "rolled zero". All sorting uses `?? -Infinity` so null-init combatants sort to bottom. `beginCombat()` validation checks for null/undefined, not falsy.

## Phase 4 - Multi-Combat, SquishText Export/Import & Polish (done)

### Multi-Combat Support
- `state.combat` (single object) → `state.combats` (array) + `activeCombatId` (transient)
- `pf_enc_combats` IndexedDB key (auto-migrates from old `pf_enc_combat` single-object key)
- `getActiveCombat()` helper replaces all direct `state.combat` references
- Combat list view: cards for each combat showing name, round, turn, alive count, player names
- Click card to resume, "Back to List" button in combat bar when multiple combats exist
- Each combat gets `id` (UUID) and `name` (auto-generated from encounter + party names)
- `endCombat()` removes from array instead of setting null
- `startCombat()` clears `activeCombatId` before switching to combat view (ensures party select shows)
- `switchView('combat')` clears `activeCombatId` when no pending encounter (ensures combat list shows)

### SquishText Import/Export
- Toolbar buttons: "Save Backup" / "Load Backup"
- Save Backup downloads `.squishtext` file (SquishText-encoded: deflate-raw → base64 → CRC32 → header)
- Load Backup uses direct file picker (no modal) - creates hidden `<input type="file">`, processes on change
- SquishText format only (no JSON fallback)
- Smart merge on import: skips duplicates by ID, keeps existing data, adds new items only
- Compression functions (`compress`, `decompress`, `crc32`) embedded directly
- Export payload: `{ version, exported, templates, encounters, parties, combats }`

### Per-Monster Import/Export
- Export button on each monster card - downloads single template as `.squishtext`
- "Import Monster" button in toolbar (visible on Monsters tab only)
- Import Monster modal with multiselect file input - processes multiple `.squishtext` files at once
- Smart merge: dedupes by ID across all selected files, skips existing templates
- Works with both full backup files and single-monster exports (reads `templates` array from payload)

### Searchable Monster Picker
- Replaced `<select>` in encounter form with text input + filtered dropdown (`.search-select`)
- `filterMonsterPicker()` filters by name (case-insensitive), `selectMonster()` sets `data-template-id`
- Dropdown closes on click outside, shows CR tag per monster

### Storage Indicator
- Footer shows "Storage: X.X KB / 2.0 GB" via `navigator.storage.estimate()`
- Auto-scales units: B → KB → MB → GB for both usage and quota
- Updated after load and debounced after saves (2s timeout)

## Phase 4.1 - Per-Party Player Lists (done)

- Removed shared player roster (`state.players` array, `pf_enc_players` storage key)
- Each party now owns its own `players[]` array - self-contained, no shared state
- Party form simplified: text input + Add button, player chips with × remove button
- Removed `togglePlayerInParty()`, `removeFromRoster()` functions
- `addPlayerToParty()` simplified - just pushes to `formData.players`, no shared roster
- New `removePlayerFromParty(idx)` - splices from `formData.players`
- CSS: `.roster-chip`/`.roster-grid` renamed to `.player-chip`/`.player-list`, removed toggle/selected styling
- Import migration: old backups with `players` array merge roster names into all parties
- Storage cleanup: `pf_enc_players` key deleted from IndexedDB on load

## Phase 4.2 - Combat Reinforcements & Modal Cleanup (done)

Replaced browser `prompt()` dialogs with proper modals and added the ability to insert templated monsters into running combat.

- **Add Combatant modal** (`showAddCombatantModal()`): Three modes - Ad-hoc (name + init), Player (name, init, AC, notes), and Monster (searchable picker + qty). Toggle group switches between modes.
- **`createMonsterCombatant(template, name)`**: Shared helper extracted from `launchCombat()`. Creates full monster combatant with rolled init and HP from `template.hpMax`.
- **`getExistingMonsterCount(combat, templateId)`**: Counts existing combatants for retroactive numbering.
- **Retroactive numbering**: When adding more of the same template, bare-named instances get renamed (e.g., "Goblin" becomes "Goblin 1" when "Goblin 2" is added).
- **Combat monster picker**: `filterCombatMonsterPicker()` / `selectCombatMonster()` with separate DOM IDs from encounter form picker.
- **Custom condition inline input**: `toggleCustomCondInput()` shows/hides a text input when "Custom..." is selected in the condition dropdown. No more `prompt()`.
- Deleted `addAdhocCombatant()`, replaced both call sites (init setup + combat bar) with `showAddCombatantModal()`.

## Phase 4.3 - View Persistence (done)

- Active tab saved to `state.preferences` in IndexedDB on every tab switch
- On page load, restores the saved view instead of defaulting to Monsters
- Added `preferences` key to `STORAGE_KEYS` and `state`
- Preferences included in backup exports and restored on import
- Valid views validated against whitelist: `templates`, `parties`, `encounters`, `combat`

## Phase 4.4 - Cleanup & Active Combatant UX (done)

### Dead Code Cleanup

Remove leftover artifacts that are no longer needed:

- **`groups`**: Dead field from pre-search monster grouping concept. Exists in `STORAGE_KEYS`, `state`, and `newTemplate()` but never read or rendered. Remove all three references and the `pf_enc_groups` IndexedDB key.
- **Audit for other dead fields/functions**: Scan for any other unused state keys, template fields, or functions that accumulated during development.

### Active Combatant Auto-Expand

The active combatant's detail panel is always expanded during their turn.

- When **Next >>** advances to a new combatant, their panel auto-expands if collapsed
- The active combatant's panel **cannot be collapsed** during their turn (disable the collapse toggle for `combat.combatants[combat.turn]`)
- When the turn advances past them, the panel collapses back (unless it was manually expanded before their turn)
- Previous turn's panel: if it was already expanded before auto-expand, leave it open. If it was auto-expanded, collapse it on turn advance.
- Track with `autoExpandedId` variable (persisted in `state.preferences`). Set when auto-expanding a previously-collapsed panel. Cleared on turn advance after collapsing.

## Phase 4.5 - Monster Descriptions & Combat Polish (done)

Two new text fields on monster templates for in-combat reference:

- **`playerDescription`** - flavor text describing the creature's appearance and behavior. What the DM reads aloud or references when a player asks "what does it look like?"
- **`dmDescription`** - DM-only lore, SRD reference text, background info, behavioral notes

### Template Form

Both fields are textareas in a new "Descriptions" section at the bottom of the template form, after Tactics & Notes.

### Combat Detail Panel

Tactics, player description, and DM description shown in a collapsible "Tactics & Descriptions" accordion at the bottom of the monster's detail panel (below concentration). Collapsed by default. Each entry displayed as a card box (dark background, rounded corners) with a Copy button for clipboard access. Only renders if at least one field (tactics, playerDescription, or dmDescription) has content. Player description labeled in accent color, DM description in warning color, tactics in muted color.

### Combat UI Polish

- Chk/Save ability buttons styled as `btn-accent` (matching the Add button under conditions)
- Status bar (`#statusEl`) reserves fixed height when empty (`border-bottom: none` instead of `display: none`) to prevent layout bounce
- Combat bar uses compact padding (`0.35rem`) and combat view has no top padding to prevent size shift on scroll with sticky positioning
- `activeCombatId` and `autoExpandedId` persisted in `state.preferences` - combat and auto-expanded panel survive page refresh
- Prev button disabled at round 1, first combatant (no prior turn to go back to)

## Phase 5 - Combat Overrides (In-Combat Monster Editing) (done)

Edit any template field on in-flight monster combatants without modifying the original template. Supports single and batch editing with sparse delta overrides.

### Core: Sparse Override System

`getTemplate(c)` returns `deepMerge(rawTemplate, c.overrides)` when overrides exist, otherwise the raw template. `getRawTemplate(c)` returns the unmerged template for edit form diffing. `resolveField()` was removed - all call sites replaced with direct template property access.

#### `deepMerge(template, overrides)`

Recursive merge producing a complete template-shaped object:
- Simple fields (string, number, boolean): override wins if present
- Objects (abilities, etc.): recursive merge
- Arrays: **two behaviors based on length**:
  - Same length as base: index-based merge. `overrides[i]` is null = use `base[i]`. `overrides[i]` exists = deep merge. Used for attacks, features, legendaryActions (index-referenced, cannot add/remove).
  - Different length from base: full replacement. Used for savingThrows, skills (name-referenced, can add/remove).

#### `sparseOverrides(template, edited)`

Produces a sparse override object by diffing an edited copy against the template:
- Simple fields: include only if value differs from template
- Objects: recurse, include only sub-keys that differ
- Arrays: **two behaviors based on length**:
  - Same length: compare by index, include only changed indices (null for unchanged). Omit array if no indices changed.
  - Different length: store the full edited array (it is a replacement).

~15-20 lines of vanilla JS. No external dependencies.

#### Integration Point

One change at each template lookup in combat rendering:

```js
// Before:
var template = state.templates.find(t => t.id === c.templateId);

// After:
var template = deepMerge(
  state.templates.find(t => t.id === c.templateId),
  c.overrides || {}
);
```

All downstream reads (`template.ac`, `template.attacks`, etc.) automatically get overridden values. No other combat rendering code changes needed.

#### What Overrides Cover

All template fields are editable: name, size, type, alignment, ac, acNote, hpMax, speed, cr, abilities, savingThrows, skills, damage resistances/immunities/vulnerabilities, condition immunities, senses, languages, passivePerception, multiattack, critRange, attacks (name, bonus, note, damages), features (name, desc, recharge, usesMax), legendaryActionBudget, legendaryActions (name, cost, desc), legendaryResistances, tactics.

#### What Overrides Do NOT Cover

Instance state stays on the combatant directly (not in overrides): `currentHp`, `tempHp`, `conditions`, `concentration`, `legendaryActionsRemaining`, `legendaryResistancesRemaining`, `featureUses`, `rollLog`, `notes`, `reactionUsed`, `isDead`. These are combat-instance state, not template-field changes.

#### Constraints

- **Index-referenced arrays (attacks, features, legendaryActions)**: Cannot add or remove elements. Array indices are referenced by combat state (`featureUses[fIdx]`, `rollAttack(cIdx, atkIdx)`, `useLegendaryAction(cIdx, laIdx)`, `rollInlineDice(..., sourceIdx)`). Only values within existing elements can be changed.
- **Name-referenced arrays (savingThrows, skills)**: Can add and remove elements. These are looked up by name (`savingThrows.find(s => s.ability === ability)`, `skills.find(s => s.name === 'Perception')`), so index changes are safe. Useful for buffs, potions, items.
- **HP interaction**: Overriding `hpMax` does not auto-adjust `currentHp`. The DM heals/damages separately via the combat screen. If `currentHp` ends up above the new `hpMax`, the HP bar shows it - the DM adjusts manually.
- **Backward compatibility**: Old combats without `overrides` work naturally: `deepMerge(template, c.overrides || {})` with empty overrides returns the template unchanged. No migration needed.

### Batch Edit Mode (Core Feature)

The primary editing interface. All editing flows through this system, whether one monster or fifty.

#### UX Flow

1. Click **Edit Mode** in the combat bar (toggle button)
2. Checkboxes appear on each monster row
3. First selection locks to that template - only same-template monsters remain selectable, others grey out
4. Select desired combatants
5. Click **Edit Selected** - opens the template form
6. Form is pre-populated with **effective values** (template merged with existing overrides)
7. Overridden fields are visually highlighted (colored border or background tint)
8. A **reset icon** appears next to each overridden field/section - click to revert that field to template value
9. Make changes, click **Apply** - `sparseOverrides(rawTemplate, formData)` produces the override, written to all selected combatants
10. If a field is changed back to the template value, it is excluded from overrides (effectively a reset)
11. The form needs both the raw template (for diffing and reset) and the merged copy (for display)
12. Cancel discards changes, exit edit mode returns to normal combat

#### Reset Flow (same workflow)

1. Enter Edit Mode, select the 10 goblins hit by Dispel Magic
2. Click Edit Selected
3. Reset the fields that were dispelled
4. Apply - overrides removed from those combatants for those fields

#### Conflicting Overrides in Batch

When selected combatants have different override values for the same field:
- Fields where all selected agree (same override or all unoverridden): **editable**, show the value
- Fields where selected disagree (different override values): **disabled**, show "varies" label
- Reset also disabled for conflicting fields (handle those individually)

### Single Monster Edit (Shortcut)

An **Edit** button on the monster's expanded detail panel. This is a shortcut that enters Edit Mode with that one combatant pre-selected and opens the form immediately. Same code path as batch edit - single edit is just a one-combatant batch edit.

### Visual Indicators

#### Initiative List Row

Combatants with active overrides show a visual indicator (badge or pip) on their row so the DM can see at a glance which monsters have been modified.

#### Detail Panel

Overridden values are highlighted wherever they appear in the detail panel - AC, abilities, attack bonuses, damage dice, feature descriptions, etc. The highlighting matches the edit form styling so the DM sees the same visual language inside and outside the editor.

### Implementation Order (all done)

1. `deepMerge()` and `sparseOverrides()` utility functions + `isFieldOverridden()` + `getRawTemplate()`
2. Modified `getTemplate()` to return merged result, removed `resolveField()` and all 5 call sites
3. Edit mode state (7 transient variables), combat bar toggle, controls lockout
4. Checkboxes with template locking, row dimming for non-selectable templates
5. Edit form (`renderEditOverrideForm()`) with revert buttons, conflict detection, save/skill add/remove
6. Save/Close/Cancel logic with dirty tracking (`editFormSnapshot` + `isEditFormDirty()`), conflict field preservation. Save persists and stays open, Close/Cancel warn if dirty.
7. Single edit shortcut: Edit button on monster detail panel
8. Visual indicators: override badge on rows, `ovh()` highlight wrapper on all detail panel fields
9. Per-item save/skill revert: match by name (ability for saves, skill name for skills), deleted items shown struck-through with restore button, added items show revert button (removes them)
10. Roll mode popup: replaced inline Dis/Adv buttons on attacks, checks, saves, and damage with click-to-popup menus (Dis/Normal/Adv for d20 rolls, Normal/Crit for damage)

## Phase 5.1 - Combat Detail Panel Redesign (done)

Restructured the monster combat detail panel for a D&D Beyond-inspired layout with rollable checks, saves, and skills.

### Left Column (the "what it does" column)
1. Hit Points (Dmg/Heal/THP) - unchanged
2. Multiattack - unchanged
3. **Features** - moved from right column (inline dice, use tracking, recharge all next to roll log)
4. Attacks - unchanged
5. Roll Log - unchanged

### Right Column (the "reference + roll" column)
1. Speed & Senses - moved to top for quick reference
2. Passives - PP (Passive Perception), PI (Passive Investigation), PIn (Passive Insight)
3. Attribute grid - 3-column, 2 rows. Each ability: label, check button (score + modifier), save button (bonus). Proficient saves highlighted in accent color.
4. Skills grid - 3-column, 6 rows. All 18 D&D skills with roll buttons. Proficient skills highlighted in accent color.
5. Legendary Actions/Resistances - unchanged

### New Code
- `SKILL_ABILITY_MAP` constant: maps each skill to its governing ability
- `calcPassiveInvestigation(t)`: 10 + Investigation bonus or INT mod
- `calcPassiveInsight(t)`: 10 + Insight bonus or WIS mod
- `rollSkillCheck(cIdx, skillIdx, mode)`: rolls 1d20 + skill bonus (proficient) or ability mod, supports Dis/Normal/Adv via roll popup

### CSS
- `.attr-grid`, `.attr-cell`, `.attr-label`, `.attr-btn` (with `.proficient` variant)
- `.attr-sub` (save sub-label between check and save buttons)
- `.save-btn` (dashed border for non-proficient, solid for proficient)
- `.skill-grid`, `.skill-cell`, `.skill-label`
- `.passive-row`, `.passive-label`
- `.combat-info`, `.combat-info-row`, `.combat-info-label`, `.combat-info-value`, `.combat-info-copy`

### Design
- Buttons fill grid cells (no floating disconnected buttons)
- Labels centered above buttons, "save" sub-label between check and save buttons
- Proficient saves: accent color, solid border. Non-proficient: muted, dashed border.
- Proficient skills: accent color border and text. Non-proficient: muted style.
- All numbers in monospace font
- Override highlighting works on new layout via `ovh()` and per-item comparison against `getRawTemplate()`

### Additional Polish (done)

#### Combat Info Header
Party name, encounter name, campaign, location, and notes displayed above the initiative tracker. Each field shown as a labeled row with a Copy button for clipboard access. Uses `copyCombatField(btn, field)`. Only renders fields that have content.

#### Color Scheme Swap
Override markers (highlight, badge, name, edit-field-modified, revert button) changed from purple to yellow (`--warning`). Multiattack box changed from yellow to purple (`--accent`). Ensures overrides are visually distinct from structural elements.

#### Per-Item Override Highlighting
Saves and skills now compare individual values against `getRawTemplate()` rather than checking `isFieldOverridden()` on the whole array. Only actually modified items get the yellow override marker, not all items in an overridden array.

#### Roll Popup Edge Detection & Stacking
`positionPopup()` clamps popup within viewport bounds (8px margin). Buttons stack vertically (`flex-direction: column`) for clearer layout.

#### Data Integrity Fixes
- `deepMerge()` handles sparse object overrides (base=array + override=non-array-object with string numeric keys)
- `setNestedValue()` creates arrays (not objects) when next path segment is numeric (root cause fix)
- `ensureArray()` safety net in `getTemplate()` normalizes all array fields after merge
- Three-layer defense ensures corrupted override data in IndexedDB doesn't crash the combat screen

#### Prev Turn Condition Reversal
- `reverseTurnStartEffects(idx)` re-increments round-based condition durations when using Prev
- Expired conditions stashed to `c._expiredConditions` before removal in `applyTurnStartEffects()`
- `reverseTurnStartEffects()` restores expired conditions from stash with duration=1

#### Ad-hoc Roll Log
Ad-hoc/lair combatant detail panels now include a roll log container for condition countdown notifications.

## Phase 5.2 - Destructible Ad-hoc Combatants (done)

Added optional HP and AC fields to ad-hoc combatants for destructible objects (doors, siege engines, gates, crystal pylons, portals, defended objectives).

### Changes
- **Add Combatant modal**: Ad-hoc mode now has optional HP and AC inputs alongside Initiative (all on one row)
- **`doAddCombatant()`**: Reads HP/AC inputs, sets `hpMax`, `currentHp`, `ac` on the combatant when provided
- **`applyDamage()`/`applyHeal()`**: maxHp resolution changed from `c.currentHp` to `c.hpMax || c.currentHp` for non-template combatants
- **Combatant row**: Ad-hoc with `hpMax` gets full HP bar (same as monsters); ad-hoc with `ac` shows in AC column
- **Detail panel**: Ad-hoc with `hpMax` gets HP display + Dmg/Heal/THP controls (same as monsters); `ac` displayed below

### Data
New optional fields on ad-hoc combatants: `hpMax` (number), `currentHp` (number), `tempHp` (number, on mutation), `ac` (number). All optional - ad-hoc without these behaves identically to before.

### Backward Compatible
Existing ad-hoc combatants in saved combats have no `hpMax` - no HP controls shown, no HP bar. No migration needed.

### Phase 5.3 - General-Purpose Dice Roller

"Roll Dice" button centered in toolbar opens a modal for rolling arbitrary dice expressions.

#### Parser
Recursive descent with operator precedence. Grammar: `expression = term ((+|-) term)*`, `term = factor ((*|/) factor)*`, `factor = '(' expression ')' | NUMBER? 'd' NUMBER | NUMBER`. Whitespace stripped before parsing. Adv/dis/advantage/disadvantage suffix extracted first (case-insensitive). Division uses `Math.floor`. Safety cap 1000 dice per group.

#### Display
Each dice group shows individual rolls (2-10 dice) or sum-in-parens (11+). Single die shows just the value. Pure-number arithmetic collapses to computed value. 1d20 with adv/dis shows both rolls and pick.

#### Modal
Input + Roll button, results area (newest first, max 50), Clear + Close buttons. Enter to roll, Escape to close, click-outside to close. Input stays after rolling (selected for quick replacement). History persists across modal close/reopen (ephemeral per session).

#### New Functions
`parseDiceExpression()`, `evalDiceNode()`, `rollDiceExpression()`, `showDiceRoller()`, `doDiceRoll()`, `renderDiceRollerResults()`. Global: `diceRollerHistory[]`.

### Phase 6 - Refactor (done)

Code quality pass. Zero user-facing behavior changes.

#### 6.1 - Null Guards (done)
Created `getCombatant(idx)` helper that validates combat exists and idx is in range. Replaced all 29 `getActiveCombat().combatants[idx]` patterns with `getCombatant(idx)` + early return. Fixed `toggleReaction()` double `getActiveCombat()` call.

#### 6.2 - Unify Dice Engines (done)
Modified `evalDiceNode()` to propagate raw `rolls` array through all node types (dice, number, paren, binop). Updated `rollDiceExpression()` to pass through `rolls`. Replaced old regex-based `rollDice()` with a thin wrapper that delegates to the new recursive descent parser. `rollAttack()` crit detection works because `result.rolls` still contains raw d20 values.

#### 6.3 - var to const/let Sweep (done)
Converted all ~220 `var` declarations to `const` (never reassigned) or `let` (reassigned). Two global vars (`diceRollerHistory`, `rollPopupState`) converted to `let`. Zero `var` remaining in JS code; CSS `var(--...)` untouched.

#### 6.4 - Consolidate Global State (done)
8 edit-mode globals (`editMode`, `editSelected`, `editLockedTemplateId`, `editFormData`, `editFormVisible`, `editRawTemplate`, `editFormSnapshot`, `editConflicts`) consolidated into `editState` object. 3 form globals (`formMode`, `formData`, `savedFormSnapshot`) consolidated into `formState` object. All 155+ references updated including inline `onchange` handlers in HTML strings.

#### 6.5 - Extract Render Helpers (done)
Three helpers extracted: `getCombatantHp(c, template)` resolves maxHp consistently, `renderHpBar(curHp, maxHp, tempHp)` deduplicates HP bar rendering (was 2 copies), `createModal(id, title, bodyHtml, opts)` unifies modal creation (was 3 copies - add combatant, import, dice roller).

#### 6.6 - Break Up Mega-Functions (done)
`renderActiveCombat()` split into `renderCombatBar()`, `renderCombatInfo()`, `renderCombatantRow()` with orchestrator. `renderCombatantDetail()` split into `renderMonsterDetail()`, `renderPlayerDetail()`, `renderAdhocDetail()` with type-routing. Template/edit forms kept intact (too tightly coupled for modest benefit).

#### 6.7 - CSS Consolidation (done)
8 utility classes added: `.flex-row`, `.flex-row-tight`, `.flex-row-end`, `.flex-col`, `.text-muted-sm`, `.text-dim-sm`, `.text-mono`, `.detail-label`. 22 inline style patterns replaced. One-off styles left inline.

### Phase 7 - 5etools Importer (done)

Implemented complete 5etools JSON import pipeline:
- Tag stripper (`strip5eToolsTags`) handling 20+ tag types with nested tag support
- Recursive entry flattener (`flattenEntries`) for structured entries (lists, items, tables)
- Field parsers: size, type, AC, speed, saves, skills, CR, resistances/immunities, alignment, initiative
- Action parser splits attacks/multiattack/features, extracts hit bonus, reach/range, damage dice+types
- Legendary action parser with continuation merging (handles MCDM villain action format)
- Spellcasting → features converter (at-will, daily, slots, recharge)
- LR extraction from traits
- Import modal toggle group UI (SquishText | 5etools) matching Add Combatant pattern
- Format auto-detection in SquishText mode (routes 5etools JSON to correct parser)
- Name-based dedup for 5etools imports

Bug fixes during testing:
- Speed: hover duplication when both `canHover` and `condition: "(hover)"` present
- Condition immunities: `|source` tags now stripped (e.g., `"dazed|FleeMortals"` → `"Dazed"`)
- Attack notes: both reach and range captured (e.g., `"reach 15 ft. or range 600 ft."`)
- Legendary continuations: entries without `Action N:` or `(Costs N Actions)` merged into previous action
- Recharge abilities: `usesMax` set to 1 so Use/Restore/Roll Recharge buttons appear
- Detail panel: `grid-template-columns: 1fr auto` → `1fr minmax(0, 320px)` to prevent right column domination
- Legendary actions/resistances moved from right column to left column for better readability
- `flattenEntries`: `type:"itemSub"` handling alongside `type:"item"`, supports `entry` (singular) field, type-specific checks moved before generic `entries.entries` fallback
- `parse5eAlignment`: axis analysis for alignment ranges (e.g., `["L","NX","C","E"]` -> "Any Evil")
- `parse5eLegendary`: cost regex now matches `(N Actions)` without requiring "Costs" prefix
- Senses: wrapped with `strip5eToolsTags()` to handle `{@variantrule}` tags
- `strip5eToolsTags`: display text support - uses 3rd segment when present (e.g., `{@variantrule Cone [Area of Effect]|XPHB|Cone}` -> "Cone")
- Import disclaimer: danger-styled warning on 5etools modal advising DMs to review imported creatures
- Em dash cleanup: 5 Unicode em dashes replaced with ASCII hyphens throughout codebase

### Phase 7 Import Testing

Tested across 11 creatures covering all major categories:
- Goblin Warrior (CR 1/4, basic)
- Scion of Memnor (CR 26, legendary)
- Xogomoc (CR 30, MCDM villain actions)
- The World Ender (CR 30, Drakkenheim epic actions, special HP)
- Elf Vampire (shapeshifter, alignment ranges, itemSub entries)
- Vampire Infernalist (spellcaster, nested entries)
- Aspect of Bahamut (mythic actions)
- Bone Swarm (swarm, choice-based damage)
- Loup Garou (lycanthrope, form restrictions)
- Illithilich (innate psionics + regular spellcasting, variable LA cost)
- Adult Red Dragon (2024 tags with display text)
- Gas Spore Fungus (low CR utility)

Known edge cases covered by import disclaimer (DM verifies after import):
- Choice-based damage (bludgeoning/piercing/slashing options) - appears as additive
- Versatile weapons (one-handed vs two-handed) - appears as single entry
- Variable legendary action costs (e.g., "Costs 1-3 Actions") - defaults to cost 1
- Mythic actions - imported as features with "(Mythic)" suffix, mythicHeader text lost
- Non-standard action economies (VA, EA) - imported as features or legendary actions

### Phase 7 Reference Documentation

#### Importer Architecture Pattern (applies to all future importers)

All importers follow the same pattern:
1. **Pure converter function**: `importXxx(json) → template[]` - takes parsed JSON, returns array of our template objects. No side effects, no DOM, no state mutation.
2. **Format detection**: `doImportMonster()` currently only handles `.squishtext` files. For Phase 7+, update it to also accept `.json` files AND clipboard paste. Detection order: try SquishText decompress first → if that fails, try JSON.parse → if valid JSON, detect which format (our native format has `version` + `templates`, 5etools has `monster` array or top-level 5etools fields like `str`/`dex`/`ac` as array).
3. **Input methods**: File upload (`.squishtext`, `.json`) AND paste textarea. Users copy JSON from 5etools via clipboard - paste is the primary UX for external imports.
4. **Smart merge**: Same ID-based dedup as existing import. Imported templates get new `uuid()` IDs (since external formats don't use our IDs).
5. **Status reporting**: Show count of imported vs skipped (by name dedup for external formats since they have no IDs - dedup by name to avoid importing the same monster twice).

#### 5etools Data Source

Users copy JSON from the 5etools website via clipboard (not file download). The site shows monster JSON and users copy it. This means:
- A single monster = a top-level JSON object (most common use case)
- A multi-monster export = `{ "monster": [ ...entries... ] }` wrapper
- Import UX should support both `.json` file upload AND clipboard paste

**Detection**: If parsed JSON has a `monster` array → 5etools multi. If it has `str` (number) + `ac` (array) → 5etools single.

#### 5etools Monster Entry - Key Fields

Verified against official JSON schema (v1.21.60 from `TheGiddyLimit/5etools-utils`) and 6 real samples: The World Ender (CR 30 aberration, homebrew), Goblin Warrior (CR 1/4, MCDM), Goblin Archer (CR 1/4, LotR homebrew), Werewolf (CR 3, XMM 2024), Vampire (CR 13, XMM 2024), Pit Fiend (CR 20, XMM 2024).

Full schema reference: `5ETOOLS_CREATURE_SCHEMA.md` (in the tools repo at `references/5ETOOLS_CREATURE_SCHEMA.md`)

Fields we care about (fields we ignore are documented in the schema reference):

```
name            string          "The World Ender"
size            string[]        ["G"] or ["S", "M"]. Codes: F/D/T/S/M/L/H/G/C/V (C=Colossal, V=Varies - homebrew only)
type            string|object   "undead" or { type: "aberration", tags: ["Epic Boss"] }
ac              array           [23] or [{ ac: 15, from: [...], condition: "..." }] or [{ special: "..." }]
hp              object          { average: 60, formula: "8d8+24" } or { special: "..." }
speed           object|int      { walk: 60, fly: 80, canHover: true, swim: 30, burrow: 20, climb: 20 } or 30 or "Varies"
speed.alternate object|null     { walk: [{ number: 40, condition: "(wolf form only)" }] } - conditional speeds
initiative      object|number   { initiative: 5, proficiency: 1, advantageMode: "adv" } or 5. Absent in older/homebrew.
str/dex/con/int/wis/cha  int|null|{special}  Top-level ability scores (not nested). Can be null or { special: "..." }.
save            object|null     { dex: "+11", int: "+10" } - string values with +/-
skill           object|null     { athletics: "+19", perception: "+19" }. May have `other` array.
senses          string[]|null   ["Truesight 120 ft."]
passive         int|string|null 29 (passive perception). Can be a string in rare cases.
resist          array|null      ["acid", "cold"] or [{ resist: [...], note: "...", preNote: "...", cond: true }] or [{ special: "..." }]
immune          array|null      damage immunities (same format as resist)
vulnerable      array|null      damage vulnerabilities (same format as resist)
conditionImmune array|null      ["charmed", "frightened"] or [{ conditionImmune: [...], note: "..." }] - same nested pattern
languages       array|null      ["Common", "Draconic"] or ["Common (can't speak in wolf form)"]
cr              string|object   "30" or "1/4" or { cr: "13", lair: "15", coven: "15", xp: 10000, xpLair: 11500 }
trait           array|null      [{ name, entries[], type?, sort? }] - passive features
action          array|null      [{ name, entries[] }] - actions (attacks mixed in)
bonus           array|null      [{ name, entries[] }] - bonus actions (separate from action[])
reaction        array|null      [{ name, entries[] }] - reactions (separate from action[])
spellcasting    array|null      [{ name, headerEntries[], recharge, displayAs, ability }] - see below
legendary       array|null      [{ name?, entries[] }] - legendary actions (name optional for header text)
legendaryActions number|null    LA budget (default 3 if legendary array exists)
legendaryActionsLair number|null  LA budget when in lair (Vampire: 4)
legendaryHeader  entry[]|null   custom legendary action header text
mythic          array|null      [{ name?, entries[] }] - mythic actions (e.g. Tiamat)
mythicHeader    entry[]|null    custom mythic action header text
```

#### Spellcasting Array (2024 format)

New in 2024 monsters. Each entry:
```
{
  name: "Charm {@recharge 5}",           // may contain recharge tag
  type: "spellcasting",
  headerEntries: ["The vampire casts..."],  // description text
  recharge: { "5": ["{@spell Charm Person|XPHB}"] },  // keyed by recharge number
  ability: "cha",                          // spellcasting ability
  displayAs: "action"|"bonus"|"legendary", // where it appears in statblock
  hidden: ["recharge"]                     // UI hints, ignore
}
```
**Handling**: Convert to features. Extract recharge from name tag. Use `headerEntries` joined as description. Place in features array regardless of `displayAs` - the DM can use them from the feature panel.

#### Structured Entries (recursive)

Entries are usually strings but can be objects:
```
{ type: "list", style: "list-hang-notitle", items: [
  { type: "item", name: "Forbiddance", entries: ["The vampire can't enter..."] },
  { type: "item", name: "Running Water", entries: ["The vampire takes 20 Acid damage..."] }
]}
```
**Handling**: Implement `flattenEntries(entries)` that recursively processes:
- `string` → use as-is
- `{ type: "item", name, entries }` → `"Name. " + entries joined`
- `{ type: "list", items }` → process each item recursively
- Any other object with `entries` → process entries recursively
- Unknown → `JSON.stringify()` fallback

#### 5etools Rich Text Tags (must strip/convert)

Entries use `{@tag content}` markup. Two attack tag formats exist across different sources:

```
--- Dice/combat tags ---
{@damage 5d10 + 10}              → "5d10 + 10"
{@dice 2d6}                      → "2d6"
{@hit 19}                        → "+19"
{@dc 27}                         → "DC 27"
{@h}                             → "Hit: " (marks hit description)
{@recharge 5}                    → "(Recharge 5-6)" - extract for recharge field
{@recharge}                      → "(Recharge 6)"

--- Attack indicators (TWO formats) ---
{@atkr m}                        → "" (2024 format: melee attack)
{@atkr r}                        → "" (2024 format: ranged attack)
{@atkr ms}                       → "" (2024 format: melee spell attack)
{@atkr rs}                       → "" (2024 format: ranged spell attack)
{@atk mw}                        → "" (older format: melee weapon attack)
{@atk rw}                        → "" (older format: ranged weapon attack)
{@atk ms}                        → "" (older format: melee spell attack)
{@atk rs}                        → "" (older format: ranged spell attack)

--- Save/effect tags (2024) ---
{@actSave con}                   → "Constitution saving throw"
{@actSaveFail}                   → "Failure:"
{@actSaveSuccess}                → "Success:"
{@actSaveSuccessOrFail}          → "Success or Failure:"

--- Reference tags (take first segment before |) ---
{@condition Grappled|XPHB}       → "Grappled"
{@spell Wish|XPHB}               → "Wish"
{@creature Zombie|MM}            → "Zombie"
{@item Shield|XPHB}              → "Shield"
{@skill Perception}              → "Perception"
{@action Disengage}              → "Disengage"
{@variantrule Hit Points|XPHB}   → "Hit Points"
{@variantrule Speed|XPHB}        → "Speed"
{@variantrule Fly Speed|XPHB}    → "Fly Speed"
{@variantrule Advantage|XPHB}    → "Advantage"
{@variantrule Disadvantage|XPHB} → "Disadvantage"
{@variantrule Resistance|XPHB}   → "Resistance"
{@variantrule Emanation [Area of Effect]|XPHB} → "Emanation"
{@table Mutations|Source}         → "Mutations"
```

**General rule**: `{@tag content|source|...}` → take `content` (first segment before `|`). Exceptions:
- `{@h}` → `"Hit: "`
- `{@recharge N}` → `"(Recharge N-6)"` - also extract for recharge field
- `{@atkr X}` / `{@atk X}` → `""` (empty string, used only for attack detection)
- `{@actSave ABILITY}` → expand ability abbreviation to full name + "saving throw"
- `{@actSaveFail}` / `{@actSaveSuccess}` / `{@actSaveSuccessOrFail}` → label text

**Attack detection**: An action entry is an attack if it contains `{@atkr` OR `{@atk` OR `{@hit`. Must check both tag formats.

Implement as `strip5eToolsTags(text)` - regex-based, handles nested tags. Run in a loop until no more `{@` found (for nested cases).

#### Field Mapping: 5etools → Our Template

| 5etools | Our Template | Transform |
|---------|-------------|-----------|
| `name` | `name` | Direct |
| `size` | `size` | Map codes and join: `["S","M"]` → `"Small/Medium"`. Codes: F→Fine, D→Diminutive, T→Tiny, S→Small, M→Medium, L→Large, H→Huge, G→Gargantuan, C→Colossal, V→Varies |
| `type` or `type.type` | `type` | Extract string, capitalize |
| `type.tags` | (discard) | Tags like "Epic Boss" - no field for this |
| `ac[0]` or `ac[0].ac` | `ac` | Number extraction |
| `ac[0].from` | `acNote` | Join array, strip tags: `["{@item leather armor\|PHB}", "{@item shield\|PHB}"]` → `"Leather Armor, Shield"` |
| `hp.average` | `hpMax` | Direct number |
| `hp.formula` | `hpFormula` | Direct string (already in NdN+M format) |
| `hp.special` | `hpFormula` (as note) | Store in hpFormula, set hpMax to 0 or parse if possible |
| `speed` | `speed` | Format: `"30 ft., fly 60 ft. (hover), climb 20 ft."`. Append `(hover)` if `canHover: true`. Include `alternate` conditions. Handle integer/`"Varies"` fallbacks. |
| `initiative` | `initBonus` | If number: use directly. If object with `initiative`: use that. If object with `proficiency`: `dexMod + proficiency * profBonusFromCR`. Absent: `dexMod` only. `advantageMode: "adv"` → set `initAdvantage: true`. |
| `str/dex/con/int/wis/cha` | `abilities` | Nest into `{ str, dex, con, int, wis, cha }` |
| `save` | `savingThrows` | Parse `{ dex: "+11" }` → `[{ ability: "dex", bonus: 11 }]` |
| `skill` | `skills` | Parse to skill array format |
| `senses` | `senses` | Join array to string |
| `passive` | `passivePerception` | Direct number |
| `resist` | `damageResistances` | Join/format to string, capitalize. Handle conditional objects. |
| `immune` | `damageImmunities` | Same format as resist |
| `vulnerable` | `damageVulnerabilities` | Same format as resist |
| `conditionImmune` | `conditionImmunities` | Join, capitalize: `"Charmed, Frightened"` |
| `languages` | `languages` | Join array or `"None"` |
| `cr` or `cr.cr` | `cr` | Direct string. `{ cr: "13", xpLair: 11500 }` → `"13"` |
| `initiative.advantageMode` | `initAdvantage` | `"adv"` → `true`, otherwise `false` |
| `trait[]` | `features[]` | Map `{name, entries[]}` → `{name, desc, recharge, uses, usesMax}`. Flatten entries recursively + strip tags. Extract recharge/uses from name. |
| `action[]` | `attacks[]` + `features[]` + `multiattack` | **Complex** - see below |
| `bonus[]` | `features[]` (appended) | Bonus actions → features with `"(Bonus Action)"` appended to name |
| `reaction[]` | `features[]` (appended) | Reactions → features with `"(Reaction)"` appended to name |
| `spellcasting[]` | `features[]` (appended) | Convert each: name (strip recharge tag) → feature name, headerEntries → desc, extract recharge from name tag |
| `legendary[]` | `legendaryActions[]` | Map entries, extract cost from name pattern `"(Costs N Actions)"` |
| `legendaryActions` | `legendaryActionBudget` | Direct number, default 3 |
| `mythic[]` | `features[]` (appended) | Mythic actions → features with `"(Mythic)"` appended to name |
| - | `legendaryResistances` | Extract from traits if "Legendary Resistance" trait exists, parse `"(N/Day)"`. Ignore lair variant. |
| - | `tactics` | `""` (empty - not in 5etools data) |
| - | `playerDescription` | `""` (empty - not in 5etools data) |
| - | `dmDescription` | `""` (empty - not in 5etools data) |
| - | `critRange` | `20` (default) |

#### Proficiency Bonus from CR (for initiative calculation)

```
CR 0-4:   +2     CR 13-16: +5
CR 5-8:   +3     CR 17-20: +6
CR 9-12:  +4     CR 21-24: +7
                  CR 25-28: +8
                  CR 29+:   +9
```
Used only when `initiative.proficiency` is present. Fractional CRs (1/8, 1/4, 1/2) → proficiency +2.

#### Action Parsing (the complex part)

The `action[]` array mixes attacks, multiattack, and non-attack abilities. Two attack tag formats exist:

**2024 format** (XMM): `{@atkr m} {@hit 5}, reach 5 ft. {@h}12 ({@damage 2d8 + 3}) Piercing damage.`
**Older format** (homebrew/3pp): `{@atk mw} {@hit 4} to hit, reach 5 ft., one target. {@h}5 ({@damage 1d6 + 2}) piercing damage.`

Detection and parsing:

1. **Multiattack**: Name starts with "Multiattack" (may have suffix like "(Vampire Form Only)") → extract `entries[0]` text (stripped) → `template.multiattack`
2. **Attack** (either format): Entry text contains `{@atkr` OR `{@atk ` OR `{@hit` → parse as attack:
   - Attack bonus: extract from `{@hit N}` → number
   - Reach/range: extract from text like `"reach 10 ft."` or `"range 80/320 ft."` → `note`
   - Damage: extract from `{@damage NdN + N}` → `damages[]` entries with type from surrounding text (word after closing paren, e.g., "Piercing", "Necrotic")
   - Multiple damage riders: Pit Fiend Fiery Mace has `{@damage 4d6 + 8}) Force damage plus 21 ({@damage 6d6}) Fire damage`
   - Parenthetical in name: `"Bite (Wolf or Hybrid Form Only)"` → strip parens, put in attack `note`
3. **Save-based actions** (2024): Entry starts with `{@actSave con} {@dc 17}` (no `{@hit}`) → these are save-based "attacks". Vampire Bite uses this. Parse as attack: no attack bonus (save-based), extract DC and damage.
4. **Non-attack actions**: Everything else → append to `features[]` with `recharge` extracted from name if present

#### Implementation Steps

1. **`strip5eToolsTags(text)`** - regex to convert all `{@tag ...}` to plain text. Loop until no `{@` remains (nested tags). Handle special cases: `{@h}` → "Hit: ", `{@actSave X}` → "X saving throw", `{@atkr X}` / `{@atk X}` → "".
2. **`flattenEntries(entries)`** - recursively process entry arrays. Strings pass through. Objects with `type: "list"` / `type: "item"` get flattened. Returns single joined string.
3. **`parse5eToolsSize(sizeArr)`** - map size codes to full names, join multiples with "/"
4. **`parse5eToolsAc(acArr)`** - extract AC number and note (strip tags from `from[]`)
5. **`parse5eToolsSpeed(speedObj)`** - format speed object to string, include `alternate` conditions
6. **`parse5eToolsSaves(saveObj)`** - parse save bonuses to our format
7. **`parse5eToolsResist(arr)`** - handle simple strings and conditional objects
8. **`parse5eToolsCr(cr)`** - handle string, fraction, or object with `cr` field
9. **`profBonusFromCr(crStr)`** - compute proficiency bonus from CR string
10. **`parse5eToolsAction(actionArr)`** - split into multiattack / attacks / features. Handle both tag formats.
11. **`parse5eToolsLegendary(legArr)`** - extract costs, map entries
12. **`parse5eToolsSpellcasting(spellArr)`** - convert to features with recharge
13. **`import5etools(json)`** - orchestrator: detect single vs array, map each entry using above helpers. Also processes `bonus[]`, `reaction[]`, `spellcasting[]`.
14. **Update `doImportMonster()`** - accept `.json` files, detect format, route to converter
15. **Update Import Monster modal** - add `.json` to file accept attribute, add paste textarea, update help text

#### Edge Cases
- `hp.special` (scaling HP like "X plus Y per player") - store formula text, set hpMax to parsed base or 0
- Conditional resistances: `[{ resist: ["bludgeoning", "piercing"], note: "from nonmagical attacks" }]` - flatten with note (may be rare in 2024 data)
- CR as object: `{ cr: "13", xpLair: 11500 }` → use `cr` field only
- CR as fraction: `"1/4"`, `"1/2"`, `"1/8"` - pass through as string, use for proficiency lookup
- Missing `legendary` array but has legendary resistance trait - still parse LR count from trait text
- `"Legendary Resistance (3/Day, or 4/Day in Lair)"` - parse first number only, ignore lair variant
- Actions with `"(1/Round)"` or `"(Recharges after a Short or Long Rest)"` in name - extract to recharge/uses
- Action names with form restrictions: `"Bite (Wolf or Hybrid Form Only)"` - preserve in note, strip from attack name
- Structured entries (nested lists/items) - recursively flatten before stripping tags
- `ac[0].from` may contain 5etools tags: `"{@item leather armor|PHB}"` → strip to `"Leather Armor"`
- Multiple sizes for shapeshifters: `["S", "M"]` → `"Small/Medium"`
- `initiative.proficiency` absent in older/homebrew data - fall back to dex mod only
- `spellcasting` array entries with `displayAs: "legendary"` - still place in features (not LA), user decides where to use them
- `bonus[]` and `reaction[]` arrays - append to features with type annotation in name
- `mythic[]` - mythic boss actions (rare, e.g. Tiamat). Append to features with "(Mythic)" annotation.
- `speed` as plain integer (walk-only shorthand) or `"Varies"` - handle all three speed formats
- `speed.canHover` - append "(hover)" to fly speed string
- `ac` with `condition` field - different AC in different forms, take first/highest
- `ac` with `{ special }` - text-only AC, store as note
- Ability scores as `null` or `{ special }` - set to 10 (default) or 0, note the special text
- `passive` as string - rare edge case, parse to number or store as note
- `conditionImmune` with nested conditional format - same pattern as resist/immune
- `resist`/`immune` with `{ special }` entries - plain text, append to string
- `resist`/`immune` with `preNote` - text before the resistance list
- `initiative.advantageMode: "adv"` → set `initAdvantage: true`
- `initiative` as plain number → use as flat init bonus directly

## Phase 8 - Export Enhancements & Import Update

### Overview

Expand monster export from single-format (SquishText only) to four formats, rebrand "SquishText" to "EncounterManager" in the UI, add a `format` watermark to exports, and update import to accept both `.squishtext` and `.json` files in EncounterManager mode.

### 8.1 - Export Modal & Format Watermark

**Current**: Export button on monster card downloads `.squishtext` immediately (one click).

**New**: Export button opens a modal with a four-segment toggle group and a Download button.

Toggle group: `[EncounterManager] [JSON] [Markdown] [Image]`

- **EncounterManager** (.squishtext) - current compressed format, for sharing/backup
- **JSON** (.json) - same envelope as SquishText but uncompressed, for scripting/AI workflows
- **Markdown** (.md) - formatted stat block text, for pasting into AI chats or notes
- **Image** (.png) - canvas-rendered D&D-style stat block card

**Format watermark**: Add `"format": "encounter-manager"` to the export envelope (both SquishText and JSON):

```json
{
  "format": "encounter-manager",
  "version": 1,
  "exported": "2026-02-22T...",
  "templates": [...]
}
```

Existing exports without `format` field still import fine (backward compat).

Save Backup also gets the `format` field in its envelope.

### 8.2 - Import Update

**Current**: Import modal toggle is `[SquishText] [5etools]`.

**New**: Toggle becomes `[EncounterManager] [5etools]`.

EncounterManager mode accepts both `.squishtext` and `.json` files in the same file picker:
- `.squishtext` files: decompress as before
- `.json` files: parse directly
- Both validated by checking for `format: "encounter-manager"` or the existing `version` + `templates` structure
- Auto-detection still routes 5etools JSON to the 5etools parser (no `format` field + has `monster` array or 5etools fields)

### 8.3 - Markdown Export

Generate a readable D&D stat block in markdown format. Pure function: `templateToMarkdown(template) → string`.

Output format (standard stat block layout):

```markdown
# Minotaur

*Large Monstrosity, Chaotic Evil*

---

**Armor Class** 14 (natural armor)
**Hit Points** 76 (9d10+27)
**Speed** 40 ft.

---

| STR | DEX | CON | INT | WIS | CHA |
|:---:|:---:|:---:|:---:|:---:|:---:|
| 18 (+4) | 11 (+0) | 16 (+3) | 6 (-2) | 16 (+3) | 9 (-1) |

---

**Skills** Perception +7
**Senses** darkvision 60 ft., passive Perception 17
**Languages** Abyssal
**Challenge** 3

---

**Charge.** If the minotaur moves at least 10 feet...

**Reckless.** At the start of its turn...

### Actions

**Multiattack.** One gore attack and one greataxe attack.

**Greataxe.** *Melee Weapon Attack:* +6 to hit. *Hit:* 2d12+4 slashing damage.

**Gore.** *Melee Weapon Attack:* +6 to hit. *Hit:* 2d8+4 piercing damage.
```

Include sections only when populated: Saving Throws, Skills, Damage Resistances/Immunities/Vulnerabilities, Condition Immunities, Senses, Languages, Features, Actions (Multiattack + Attacks), Legendary Actions, Reactions/Bonus Actions (features with those annotations).

Downloaded as `MonsterName.md`.

### 8.4 - Image Export (Canvas Stat Block)

Render the monster stat block as a PNG image using the Canvas API. Pure function: `templateToCanvas(template) → canvas`.

**Visual style**: Classic D&D stat block aesthetic - parchment/cream background, dark red header bars, serif font for body text, bold for labels.

**Layout sections** (top to bottom):
1. Name + size/type/alignment header (red background, white text)
2. AC, HP, Speed (red divider above)
3. Ability score table (red divider above)
4. Proficiencies, resistances, senses, languages, CR (red divider above)
5. Features (red divider above)
6. Actions header + multiattack + attacks (red divider above)
7. Legendary Actions (if any, red divider above)

**Implementation approach**:
- Create an offscreen canvas
- First pass: measure all text to calculate total height (dynamic - varies by content)
- Second pass: draw everything at calculated positions
- Use `canvas.toBlob('image/png')` to trigger download

**Fonts**: Use system serif (Georgia/Times) for body, system sans-serif for headers. No web fonts needed.

**Width**: Fixed (e.g., 400px), height dynamic based on content.

Downloaded as `MonsterName.png`.

## Future Phases

### Phase 9 - CritterDB Importer
- Discovery: investigate CritterDB JSON export format
- Data mapping: map CritterDB fields to canonical template schema
- Pure function: `importCritterDB(json) → template[]`
- UI: integrate into Import Monster modal

### Phase 10 - Bestiary Builder Importer
- Discovery: investigate Bestiary Builder JSON export format
- Data mapping: map fields to canonical template schema
- Pure function: `importBestiaryBuilder(json) → template[]`

### Backlog (unscheduled)
- Surprise round handling
- Dynamic notes/riders with round expiry
- Damage log (collapsible panel)
- Campaign/encounter filtering (dropdown or chips in encounter list)

## Key Architecture Decisions

### Sparse Delta Pattern
Combat instances only store what differs from the template. `getTemplate(c)` returns `deepMerge(rawTemplate, c.overrides)` when overrides exist, otherwise the raw template. `sparseOverrides(base, edited)` produces minimal delta objects. Overrides mirror the template structure but only contain changed values. This keeps combat state small and allows template edits to propagate to non-overridden fields. Array merge behavior differs by reference style: same-length = index-based sparse (attacks/features/LAs), different-length = full replacement (saves/skills).

### Single-File, No Dependencies
Same as all PromptFerret tools. All CSS and JS inline in `index.html`. Works from `file://` and GitHub Pages. No build step.

### Event Handler Safety
Never interpolate user strings into inline `onclick`/`onchange` handlers. Use UUIDs, numeric indices, or hardcoded strings only. User input reads from DOM via `this.value`. See CLAUDE.md for full details.

### Roll Log Persistence
Roll logs are stored on combatant instances and saved to localStorage. They survive page refreshes, panel toggles, and re-renders. Cleared only when that combatant's turn starts again. This is intentional - DMs need to reference past rolls when players contest results or use reactions retroactively (Silvery Barbs, Shield, etc.).

### Combat State Machine
`state.combats` is an array of combat objects. `activeCombatId` (persisted in `state.preferences`) selects which combat is displayed. `getActiveCombat()` returns the selected combat or null. `round: 0` = initiative setup phase. `round: 1+` = active combat. Combat view dispatches: empty → combat list → party select → init setup → active combat.
