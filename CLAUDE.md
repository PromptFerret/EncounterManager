# EncounterManager

## Text Rules

**ASCII only for punctuation.** Never use em dashes (`U+2014`), en dashes (`U+2013`), curly/smart double quotes (`U+201C`, `U+201D`), or curly/smart single quotes (`U+2018`, `U+2019`). Use the plain keyboard equivalents: `-`, `"`, `'`. This applies to all files - code, documentation, comments, strings, and markup.

## What This Is
A single self-contained HTML file (`index.html`) for D&D encounter management and initiative tracking. DMs build monster templates, organize parties, construct encounters, and run combat - all in the browser with IndexedDB persistence (auto-migrates from localStorage, falls back to localStorage if IndexedDB unavailable). No server, no accounts, no dependencies.

## User Documentation

The `docs/` folder contains user-facing documentation rendered via MarkdownSite cross-repo links (`chrome=false` mode). Each doc has its own breadcrumb navigation.

| File | Topic |
|------|-------|
| `docs/INDEX.md` | Documentation home - quick start, guide links |
| `docs/MONSTER_TEMPLATES.md` | Building and editing monster stat blocks |
| `docs/PARTIES_AND_ENCOUNTERS.md` | Setting up parties and encounters |
| `docs/RUNNING_COMBAT.md` | Core combat loop - initiative, turns, HP, attacks |
| `docs/COMBAT_DEEP_DIVE.md` | Conditions, concentration, legendaries, feature tracking |
| `docs/IMPORT_EXPORT.md` | Backups, restoring, sharing, SquishText format, 5etools import |
| `docs/MONSTER_FORMAT.md` | JSON schema reference for external monster creation |

Docs are linked from the app footer and README. **When features, schemas, or usage change, update all affected files**: `docs/` files, this `CLAUDE.md`, and `PLAN.md`. Verify accuracy before committing - stale docs cause bugs in future sessions.

## Code Map

The file is ~5300+ lines. Rough section layout:

| Section | Contents |
|---------|----------|
| CSS | Full theme, button classes, searchable select, modal overlay, getting started modal (`.modal-getting-started`, `.getting-started-grid`, `.getting-started-card`, `.getting-started-detail`, `.getting-started-back`, `.getting-started-footer`, `.getting-started-intro`), combat list cards, condition/concentration chips, LA/LR badges, turn-notify pulse, attribute grid (`.attr-grid`, `.attr-btn`, `.attr-cell`, `.attr-sub`, `.save-btn`), skill grid (`.skill-grid`, `.skill-cell`), passive row (`.passive-row`), combat info header (`.combat-info`, `.combat-info-row`, `.combat-info-label`, `.combat-info-value`, `.combat-info-copy`), utility classes (`.flex-row`, `.flex-row-tight`, `.flex-row-end`, `.flex-col`, `.text-muted-sm`, `.text-dim-sm`, `.text-mono`, `.detail-label`), responsive breakpoints |
| HTML | Header, toolbar (nav tabs + Save Backup/Load Backup/Import Monster buttons), status bar, 4 view containers, footer with Getting Started link + storage indicator |
| Constants & State | `ABILITIES`, `CONDITIONS`, `SKILLS`, `SKILL_ABILITY_MAP`, `SIZES`, `STORAGE_KEYS`, `state`, `formState`, `editState`, `activeCombatId`, `getActiveCombat()`, `getCombatant()`, `load()`/`save()`, `uuid()`, `esc()`, `modStr()` |
| Dice Engine | `rollDice(notation, opts)` (thin wrapper), `renderDiceText()`, `rollInlineDice()` (combat), `parseDiceExpression()`, `evalDiceNode()`, `rollDiceExpression()` (unified engine) |
| Compression | CRC32, `compress()`, `decompress()` - SquishText-compatible, embedded for import/export |
| Combat Helpers | `saveCombat()`, `getTemplate()`, `getRawTemplate()`, `ensureArray()`, `deepMerge()`, `sparseOverrides()`, `isFieldOverridden()` |
| View Management | `switchView()`, `handleNew()`, `render()` dispatcher |
| Monster Templates | List, form, CRUD, passive perception, crits-on field |
| Encounters | List, form, CRUD, searchable monster picker, delete (clears linked combats) |
| Parties | List, form, CRUD, per-party player list |
| Combat Entry | `startCombat()`, party select cards, `launchCombat()` |
| Combat List | `renderCombatList()`, `resumeCombat()` - multi-combat selector |
| Initiative Setup | `renderInitiativeSetup()`, `beginCombat()` |
| Edit Mode (Combat Overrides) | `exitEditMode()`, `toggleEditMode()`, `toggleEditSelect()`, `openEditForm()`, `renderEditOverrideForm()`, `editField()`, `applyEditOverrides()`, `closeEditOverrides()`, `cancelEditOverrides()`, `editSingleCombatant()`, `ovh()` (override highlight wrapper), `isEditFormDirty()`, `editFormSnapshot`, conflict detection, field revert, per-item save/skill revert (`isSaveItemModified`, `revertSaveItem`, `restoreDeletedSave`, `isSkillItemModified`, `revertSkillItem`, `restoreDeletedSkill`) |
| Render Helpers | `getCombatantHp(c, template)`, `renderHpBar(curHp, maxHp, tempHp)`, `createModal(id, title, bodyHtml, opts)` |
| Getting Started | `isDataEmpty()`, `showGettingStarted()` (grid view modal), `showGettingStartedDetail(section)` (detail view), `loadSampleData()` (imports embedded squishtext payload via merge logic), `SAMPLE_DATA_PAYLOAD` (constant) |
| Active Combat | `renderActiveCombat()` (orchestrator), `renderCombatBar()`, `renderCombatInfo()`, `renderCombatantRow()`, `renderCombatantDetail()` (router), `renderMonsterDetail()`, `renderPlayerDetail()`, `renderAdhocDetail()`, `showAddCombatantModal()` / `doAddCombatant()` |
| Condition Tracking | `updateConditionFields()`, `toggleCustomCondInput()`, `addCondition()`, `removeCondition()` |
| Concentration | `setConcentration()`, `dropConcentration()` |
| HP Tracking | `applyDamage()` (with concentration DC warning), `applyHeal()`, `applyTempHp()` |
| Feature Use Tracking | `useFeature()`, `restoreFeature()`, `rollRecharge()` |
| Legendary Actions | `useLegendaryAction()`, `useLegendaryResistance()`, `restoreLegendaryResistance()`, `restoreLegendaryAction()` |
| Roll Mode Popup | `showRollPopup()`, `showDmgPopup()`, `fireRollPopup()`, `fireDmgPopup()`, `hideRollPopup()`, `positionPopup()` - click Atk/Chk/Save/Dmg to get Dis/Normal/Adv or Normal/Crit popup |
| Ability Check/Save/Skill | `rollAbilityCheck(cIdx, ability, mode)`, `rollSavingThrow(cIdx, ability, mode)`, `rollSkillCheck(cIdx, skillIdx, mode)` - mode: undefined, `'advantage'`, `'disadvantage'` |
| Attack Rolling | `rollAttack()`, `rollDamageForAttack()`, `renderRollLog()` |
| Turn Management | `applyTurnStartEffects()`, `reverseTurnStartEffects()`, `nextTurn()`, `prevTurn()`, `toggleReaction()` |
| Mid-Combat Editing | `editInit()`, `killCombatant()`, `reviveCombatant()`, `removeCombatant()`, `endCombat()`, `editSingleCombatant()` |
| 5etools Importer | `strip5eToolsTags()`, `flattenEntries()`, `parse5eSize/Type/Ac/Speed/Saves/Skills/Cr/Resist/Alignment/Initiative()`, `extractRechargeFromName()`, `parse5eActions()`, `parse5eLegendary()`, `parse5eSpellcasting()`, `extractLegendaryResistances()`, `import5etools()`, `detect5eToolsFormat()`, `profBonusFromCr()`, `SIZE_MAP` |
| Import/Export | `exportData()` (Save Backup), `showImportDialog()` (Load Backup - direct file picker), `exportMonster()` (per-monster), `showImportMonsterDialog()` / `switchImportMode()` / `doImportMonster()` (Import Monster modal, toggle group) |
| Storage Indicator | `updateStorageInfo()` - measures app data from serialized state keys, quota from `navigator.storage.estimate()`, auto-scales units (B/KB/MB/GB) |
| Init | `load().then(() => { switchView(savedPref) or render(); updateStorageInfo(); if (isDataEmpty()) showGettingStarted(); })` |

*Line numbers are approximate and shift as code is added.*

## Architecture

### Views
Four top-level views, switched via nav tabs with show/hide divs:

| View | Purpose |
|------|---------|
| **Monsters** | CRUD for reusable monster templates |
| **Parties** | Manage player groups (each party has its own player list) |
| **Encounters** | Build encounters from templates (monsters only, no players) |
| **Combat** | Initiative tracking, rolling, HP/condition management |

### Render Dispatcher
The main `render()` function checks which view is active and delegates:
- Templates/Encounters/Parties: calls `renderTemplateList()` or `renderTemplateForm()` (etc.) based on whether `editingId` is set
- Combat: calls `renderCombatView()`, which further dispatches based on combat sub-state (empty → party select → init setup → active combat)

### Form Editing Pattern
Forms use `formState` object:
- `formState.mode` - null = list, 'template'/'encounter'/'party' = editing
- `formState.data` - current form state object (mutated by UI handlers)
- `formState.savedSnapshot` - JSON snapshot taken at form open/save for dirty detection
- Dirty check: `JSON.stringify(formState.data) !== JSON.stringify(formState.savedSnapshot)`

**Important**: This pattern is used for template/encounter/party forms ONLY. Combat edits are direct mutations on `state.combat.combatants[]` + `saveCombat()`. Do not use `formState.data` inside combat code. The combat override edit form uses separate `editState` object (`editState.mode`, `editState.selected`, `editState.lockedTemplateId`, `editState.formData`, `editState.visible`, `editState.rawTemplate`, `editState.formSnapshot`, `editState.conflicts`) to avoid collision.

### Form Pattern
All forms (template, party, encounter) follow Save/Close/Cancel with dirty tracking:
- **Save**: persists to localStorage, stays in form, flashes status bar green
- **Close**: returns to list view (warns if dirty)
- **Cancel**: discards changes (warns if dirty)

### State Management
```javascript
let state = { templates: [], encounters: [], parties: [], combats: [], preferences: { view: 'templates' } };
let formState = { mode: null, data: null, savedSnapshot: null };
let editState = { mode: false, selected: [], lockedTemplateId: null, formData: null, visible: false, rawTemplate: null, formSnapshot: null, conflicts: new Set() };
let activeCombatId = null; // persisted in state.preferences - ID of the combat being viewed
let autoExpandedId = null; // persisted in state.preferences - combatant ID auto-expanded on turn start
```
- `state.combats` is an array of combat objects (supports multiple simultaneous combats)
- `getActiveCombat()` returns the combat matching `activeCombatId`, or null
- Each party owns its own `players[]` array (self-contained - no shared roster)
- `state.encounters` contain only monsters (no players) - party is selected at combat start
- Every mutation triggers `save(key)` immediately (fire-and-forget async IndexedDB write; in-memory `state` is the source of truth)

### Storage (IndexedDB)
- **Database**: `pf_encounter`, version 1, single object store `state`
- **Keys** (same `pf_enc_` namespace): `pf_enc_templates`, `pf_enc_encounters`, `pf_enc_parties`, `pf_enc_combats`, `pf_enc_preferences`
- **`load()`** is async - reads from IndexedDB, auto-migrates from localStorage on first run (copies data, then clears localStorage)
- **`save(key)`** is fire-and-forget - kicks off IndexedDB write without awaiting, callers don't need to change
- **Fallback**: if IndexedDB is unavailable (`file://` + Safari edge cases), falls back to localStorage silently via `_useLocalStorage` flag
- **Init**: `load().then(render)` - async load completes before first render
- **Capacity**: ~50MB+ vs localStorage's ~5-10MB

### Data Schemas

**Monster Template** - canonical definition with all stats, attacks, features, legendary actions, lair actions. Key fields: `abilities` (object with str/dex/con/int/wis/cha), `attacks[]` (each with `bonus`, `note`, `desc` for full attack text, and `damages[]` array of `{dice, type, note}`), `features[]` (with optional `recharge` and `uses/usesMax`), `legendaryActions[]` (each with `name`, `cost`, `desc`), `lairActions[]` (each with `name`, `desc` - reference only, no budget/tracking), `passivePerception` (null = auto-calc from WIS mod or Perception skill), `critRange` (default 20, customizable for homebrew), `playerDescription` (flavor text - what players see), `dmDescription` (lore/SRD reference for the DM), `source` (publication reference, e.g., "MM 2014 p166"), `gear` (equipment carried, e.g., "chain shirt, shield, scimitar").

**Encounter** - `{ id, name, location, campaign, notes, monsters: [{ templateId, qty }] }`. No players - party selected at combat time.

**Party** - `{ id, name, players: ["name1", "name2"] }`. Players are strings (names only), managed per-party. No shared roster.

**Combat State** - `{ id, name, encounterId, partyId, round, turnIndex, combatants[], active }`. `round: 0` = initiative setup phase. `round: 1+` = active combat. Multiple combats can exist simultaneously in `state.combats[]`.

**Combatant Instance** - sparse delta pattern. Instances only store overrides from template defaults. Value resolution: `getTemplate(c)` returns `deepMerge(rawTemplate, c.overrides)` if overrides exist, otherwise raw template. All combat code uses `getTemplate(c)` which automatically returns the merged result.

Monster: `{ id, templateId, name, type:'monster', init, initRollText, currentHp, rollLog[] }`
Player: `{ id, name, type:'player', init, ac }` - init entered manually, AC editable in detail panel.
Ad-hoc: `{ id, name, type:'adhoc', init, notes, rollLog[] }` - lair actions, environment effects, destructible objects. Has a roll log for condition countdown notifications. Optional fields: `hpMax`, `currentHp`, `tempHp` (for destructible objects with HP - doors, siege engines, gates), `ac` (armor class). When `hpMax` is set, gets full HP bar on row and Dmg/Heal/THP controls in detail panel. When `ac` is set, shows in AC column.

Fields added on mutation only: `tempHp`, `overrides`, `reactionUsed`, `notes`, `dead`, `rollLog[]`, `_expanded` (transient UI flag), `featureUses` (sparse map: featureIndex → remaining), `legendaryActionsRemaining`, `legendaryResistancesRemaining`, `conditions[]`, `concentration` (string or null), `_expiredConditions[]` (stash for Prev turn reversal - conditions that expired on turn start, restored if DM goes back).

**Condition** - `{ name, durationType, duration?, saveDC?, saveAbility?, source? }`. Three types:
- `durationType: 'rounds'` - has `duration` (integer), auto-decrements on turn start, auto-expires at 0
- `durationType: 'save'` - has `saveDC` + `saveAbility`, system reminds DM at turn start, DM removes manually
- `durationType: 'indefinite'` - no auto-expiry, DM removes manually

### Combat Flow
1. DM clicks "Combat" on an encounter
2. App validates monsters exist and at least one party is saved
3. `pendingCombatEncounterId` is set (transient variable, not persisted) - bridges encounter list to combat view
4. Combat view shows party selection cards
5. DM picks a party → `launchCombat(partyId)` creates a new combat in `state.combats[]`, sets `activeCombatId`, clears `pendingCombatEncounterId`
6. Initiative setup: monsters pre-rolled (with roll breakdown shown), players blank
7. DM enters player inits, clicks "Begin Combat" → sorts descending, round 1 starts
8. Active combat: expandable combatant rows with HP, attacks, roll log, conditions, concentration

### Combat Sub-States

| State | Condition | Renders |
|-------|-----------|---------|
| Empty | No combats, no `pendingCombatEncounterId` | "Go to Encounters..." message |
| Party Select | `pendingCombatEncounterId` set | Party selection cards |
| Combat List | `state.combats.length > 0`, no `activeCombatId` | Cards for each combat (click to resume) |
| Initiative Setup | Active combat, `round === 0` | Editable init list + "Begin Combat" |
| Active Combat | Active combat, `round >= 1` | Turn tracker with expandable rows |

## Key Functions

### Dice Engine
Single unified engine: `parseDiceExpression()` + `evalDiceNode()` + `rollDiceExpression()` (recursive descent parser with operator precedence). `rollDice(notation, opts)` is a thin wrapper for backward compatibility - returns `{ rolls, modifier, total, text }`. The `rolls` array contains raw die values (used by `rollAttack()` for crit/fumble detection). Supports advantage/disadvantage for 1d20 rolls only - `evalDiceNode()` returns `modeApplied: true` when adv/dis was actually used (exactly 1d20), and `rollDiceExpression()` only shows the `(adv)`/`(dis)` label when `modeApplied` is true. Never interprets results (no "hit"/"miss").

### Inline Dice
- `renderDiceText(text, combatantIdx, sourceType, sourceIdx)` - scans text for dice notation patterns (`NdN+M`), returns HTML with clickable `.dice-link` spans. `sourceType` (`'feature'` or `'la'`) and `sourceIdx` are optional - when provided, roll log entries include the feature/LA name.
- `rollInlineDice(combatantIdx, notation, sourceType, sourceIdx)` - rolls dice from inline text, resolves source name from template, appends to rollLog with label like "Fire Breath: 2d6+3", live-updates via `renderRollLog()`

### Attack Rolling
- `rollAttack(cIdx, aIdx, mode)` - mode: undefined (normal), `'advantage'`, `'disadvantage'`. Crit detection uses `template.critRange` (default 20). Appends to combatant's `rollLog[]`.
- `rollDamageForAttack(cIdx, aIdx, crit)` - crit doubles dice count (NdM → 2NdM), not modifiers. Appends to `rollLog[]`.
- `renderRollLog(cIdx)` - live-updates the roll log DOM without full re-render.

### Roll Log
- Stored on each combatant as `rollLog[]` array, persisted to localStorage.
- Entries stack chronologically - attacks, damage, crits, inline dice, recharge rolls, condition notifications all accumulate.
- Cleared at the start of that combatant's next turn (in `applyTurnStartEffects()`).
- Survives page refreshes, panel close/open, re-renders.

### HP Tracking
- Single input + 3 buttons: Dmg (red), Heal (green), THP (yellow).
- `applyDamage(idx)` - THP absorbed first, overflow to HP, auto-mark dead at 0. Warns about concentration save DC if concentrating.
- `applyHeal(idx)` - cap at maxHp, auto-revive if healed above 0.
- `applyTempHp(idx)` - THP doesn't stack, takes higher value (D&D rules).

### Initiative Values
- Init can be a number (including 0) or `null` (not yet set). These are distinct states - `0` displays as "0", `null` displays as empty input.
- All init comparisons use `c.init !== null && c.init !== undefined` (never `!c.init`, which treats 0 as falsy).
- All sorting uses `(c.init ?? -Infinity)` so null-init combatants sort to the bottom.
- `setCombatantInit()` and `editInit()` convert empty string input to `null`, numeric input to number.
- `beginCombat()` validates that all players have non-null init before starting.

### Init Editing
- `editInit(idx, value)` - if current combatant drops init, their turn ends and combat advances to next (via `applyTurnStartEffects()`). Non-current combatant edits preserve the current turn.

### Turn-Start Logic
`applyTurnStartEffects(idx)` is the consolidated turn-start hook, called from both `nextTurn()` and `editInit()` (turn-advance branch). It:
- Resets `reactionUsed` on the combatant
- Clears `rollLog`
- Adds recharge prompts for depleted features with `recharge` (DM-initiated - notification only, DM clicks Roll Recharge in detail panel)
- Recharges legendary actions to full budget
- Decrements condition round durations (auto-expires at 0 with notification). Expired conditions are stashed to `c._expiredConditions` before removal (for Prev reversal).
- Adds save reminders for save-based conditions

`reverseTurnStartEffects(idx)` is the undo hook, called from `prevTurn()` before moving the turn index back. It:
- Re-increments round-based condition durations (undoing the decrement)
- Restores any expired conditions from `c._expiredConditions` stash (setting duration back to 1)
- Clears the `_expiredConditions` stash after restoring

### Feature Use Tracking
- `useFeature(cIdx, fIdx)` / `restoreFeature(cIdx, fIdx)` - increment/decrement feature uses, stored in `c.featureUses` sparse map
- `rollRecharge(cIdx, fIdx)` - DM-initiated: rolls 1d6, if >= threshold restores uses. Result shown in roll log.

### Legendary Actions & Resistances
- `useLegendaryAction(cIdx, laIdx)` - deducts cost from `c.legendaryActionsRemaining`, logs LA name to rollLog, disables button if insufficient budget
- `useLegendaryResistance(cIdx)` - decrements `c.legendaryResistancesRemaining`, logs to rollLog
- `restoreLegendaryAction(cIdx)` - increments LA remaining (capped at budget), for DM take-backs
- `restoreLegendaryResistance(cIdx)` - increments LR remaining (capped at max), for DM take-backs
- LA/LR badges shown on combatant rows (e.g., "LA:2/3 LR:1/3")
- LA budget auto-recharges at start of monster's turn
- Restore LA button in section header, Restore LR button next to Use LR

### Roll Mode Popup
All d20 rolls (attacks, ability checks, saving throws, skill checks) and damage rolls use a popup menu instead of inline buttons. Clicking [Atk], [Chk], [Save], or a skill button shows a popup with Disadvantage / Normal / Advantage (stacked vertically). Clicking [Dmg] shows a popup with Normal / Crit (also stacked). The popup is a single shared `#rollModePopup` div repositioned on each click via `positionPopup()`, which clamps to viewport bounds (8px margin) so the popup never clips off-screen. `showRollPopup()` renders Dis/Normal/Adv buttons, `showDmgPopup()` renders Normal/Crit buttons. Click outside to dismiss. Callback state stored in `rollPopupState` (transient, not persisted).

### Ability Check/Save Rolling
- `rollAbilityCheck(cIdx, ability, mode)` - rolls 1d20 + ability modifier, supports advantage/disadvantage via `mode` parameter, appends to rollLog
- `rollSavingThrow(cIdx, ability, mode)` - rolls 1d20 + save bonus (proficient if in template.savingThrows) or ability modifier, supports advantage/disadvantage via `mode` parameter, appends to rollLog

### Condition Tracking
- Conditions work on ALL combatant types (monsters, players, ad-hoc)
- Standard D&D conditions list + custom text option for non-standard effects
- Three duration types: rounds (auto-decrement), save-based (DM reminder), indefinite (manual remove)
- Condition chips visible on combatant rows without expanding
- `addCondition(idx)` reads from inline form inputs (select + duration type dropdown + fields)
- `removeCondition(idx, condIdx)` - splices out with × button on chip

### Concentration
- Works on monsters and players (not ad-hoc)
- `setConcentration(idx, spellName)` / `dropConcentration(idx)`
- Concentration chip shown on combatant row
- `applyDamage()` warns with CON save DC when concentrating combatant takes damage (DC = max(10, damage/2))

### Passive Scores
- `calcPassivePerception(t)` - 10 + Perception skill bonus (or WIS mod if no Perception skill)
- `getPassivePerception(t)` - returns manual override if set, otherwise auto-calc
- `calcPassiveInvestigation(t)` - 10 + Investigation skill bonus (or INT mod)
- `calcPassiveInsight(t)` - 10 + Insight skill bonus (or WIS mod)
- `updatePPDisplay()` - refreshes PP display when WIS score changes mid-edit

### Skill Check Rolling
- `rollSkillCheck(cIdx, skillIdx, mode)` - looks up skill name from `SKILLS[skillIdx]`, finds skill bonus from template (or falls back to ability modifier via `SKILL_ABILITY_MAP`), supports advantage/disadvantage, appends to rollLog with proficiency marker
- `SKILL_ABILITY_MAP` - maps each of the 18 D&D skills to its governing ability (e.g., `'Athletics': 'str'`, `'Stealth': 'dex'`)

### HTML Escaping
`esc(s)` escapes `& < > "` for safe HTML rendering. **Never put user-provided strings into inline onclick handlers** - use array indices or UUIDs instead. The apostrophe bug (e.g., "D'Amore" breaking `onclick="fn('D'Amore')"`) is why all chip handlers use numeric indices.

## Inline Event Handler Safety

All `onclick`/`onchange` handlers that interpolate variables use ONLY:
- **UUIDs** (`t.id`, `e.id`, `p.id`) - safe, no special chars
- **Numeric indices** (`i`, `j`, `idx`) - safe
- **Hardcoded strings** (`'str'`, `'advantage'`, etc.) - safe

User input flows through `this.value` in onchange handlers (reads from DOM element, never interpolated as string literal). This pattern must be maintained for any new UI that references user data in event handlers.

**Exception for inline dice**: `renderDiceText()` interpolates dice notation strings (e.g., `'2d6+3'`) into onclick handlers. This is safe because the notation comes from `esc()`-escaped template data and the regex only matches `\d+d\d+([+-]\d+)?` patterns.

## Encounter/Combat Lifecycle
- Deleting an encounter removes all combats linked to that encounter from `state.combats`.
- Multi-combat supported: `state.combats` is an array, `activeCombatId` selects which one is displayed.
- Combat list view shows all active combats when none is selected.
- "Back to List" button appears in the combat bar when multiple combats exist.

## Testing
- No build step, no test suite. Open `index.html` in a browser.
- Inspect IndexedDB in DevTools: Application > IndexedDB > `pf_encounter` > `state`
- Check storage capacity: `navigator.storage.estimate()` in console
- Works from `file://` - no server needed (falls back to localStorage if IndexedDB unavailable).
- Works on GitHub Pages at `https://promptferret.github.io/EncounterManager/`

## Phased Build Status

- **Phase 1** (done): Monster CRUD, party CRUD, encounter CRUD, dice engine, form dirty tracking, status flash, PP auto-calc
- **Phase 2** (done): Combat core - initiative rolling with roll text display, turn management, HP tracking (single input + Dmg/Heal/THP), attack rolling (normal/adv/dis), crit damage rolling, crit/fumble highlighting (nat 20/nat 1), stacking roll log (persisted, clears on turn start), expandable combatant rows with column headers, ad-hoc combatants (sorted on add), reaction toggle, player AC, multiattack styling, init drop ends turn
- **Phase 3** (done): Crit range on template (customizable crit threshold, labeled "Crits On"), full feature/trait text display in combat with inline clickable dice rolling (roll log labels include source feature/LA name), feature use tracking with DM-initiated recharge rolls, legendary actions (budget tracking, use/restore buttons, auto-recharge on turn) and resistances (use/restore counter), ability check/save rolling from detail panel, condition/effect tracking (rounds/save-based/indefinite, all combatant types, custom effects, condition chips on rows, auto-decrement), concentration tracking with damage DC warnings, turn-start notification badges on combatant rows
- **Phase 3.5** (done): IndexedDB migration (auto-migrates from localStorage, localStorage fallback), no-cache meta tags, init 0 display fix
- **Phase 4** (done): Multi-combat support (combats array + combat list selector UI with player names), SquishText import/export (.squishtext format, smart merge by ID, per-monster export/import with multiselect), searchable monster picker in encounter form, storage indicator in footer (auto-scales B/KB/MB/GB, shows quota)
- **Phase 4.1** (done): Per-party player lists (removed shared roster), toolbar UX (Save Backup/Load Backup/Import Monster), direct file picker for Load Backup (no modal)
- **Phase 4.2** (done): Combat reinforcements - replaced browser `prompt()` with proper modal for adding combatants (three modes: ad-hoc, player, or templated monsters with full stat blocks). Searchable monster picker in modal (separate DOM IDs from encounter form picker). Retroactive numbering when adding more of the same template. Replaced custom condition `prompt()` with inline text input. Extracted `createMonsterCombatant()` helper from `launchCombat()`.
- **Phase 4.3** (done): View persistence - active tab saved to `state.preferences` in IndexedDB, restored on load. Included in backup export/import.
- **Phase 4.4** (done): Cleanup & active combatant UX - removed dead `groups` field and unused `damageLog` from combat state, IndexedDB/localStorage cleanup for old keys. Auto-expand active combatant panel on turn start (auto-collapse previous), prevent collapse during active turn.
- **Phase 4.5** (done): Monster descriptions and combat polish. Added `playerDescription` and `dmDescription` fields on templates. Tactics, player description, and DM description shown in a collapsible "Tactics & Descriptions" accordion at the bottom of the combat detail panel (collapsed by default), each as a card box with a Copy button. Chk/Save ability buttons styled as `btn-accent`. Status bar reserves fixed height (no layout bounce). Combat bar compact sizing with no top padding on combat view. `activeCombatId` and `autoExpandedId` persisted in preferences (survive refresh). Prev button disabled at round 1 first combatant.
- **Phase 5** (done): Combat overrides - in-combat monster editing with sparse delta overrides (`deepMerge` + `sparseOverrides`). Single edit, batch edit mode with checkbox selection (same-template only), per-field revert with per-item revert for saves/skills (match by name, deleted items shown struck-through with restore button), visual indicators for overridden values (yellow badge on row, yellow highlight on modified detail panel fields with per-item granularity for saves/skills). Saves/skills can be added/removed (name-referenced); attacks/features/legendary actions cannot (index-referenced). Edit mode disables combat controls (Prev/Next/+Add/End Combat). Edit form uses Save/Close/Cancel with dirty tracking (same pattern as template forms). Roll mode popup: all d20 rolls (Atk/Chk/Save/Skills) and damage rolls (Dmg) now use a click-to-popup menu (Dis/Normal/Adv for d20, Normal/Crit for damage) stacked vertically, with viewport edge clamping.
- **Phase 5.1** (done): Combat detail panel redesign and polish. Left column: HP, features (moved from right), attacks, roll log. Right column: speed/senses, passives (PP/PI/PIn), 3-column attribute check/save grid (D&D Beyond-inspired, proficient saves highlighted with accent color and solid border, non-proficient saves show dashed border), 3-column skill grid (all 18 skills with roll buttons, proficient skills highlighted), legendary actions/resistances. New: `SKILL_ABILITY_MAP`, `calcPassiveInvestigation()`, `calcPassiveInsight()`, `rollSkillCheck()`. CSS: `.attr-grid`, `.attr-btn`, `.attr-sub`, `.save-btn`, `.skill-grid`, `.skill-cell`, `.passive-row`. Additional polish: combat info header (party/encounter/campaign/location/notes with per-field Copy buttons above initiative tracker), color scheme swap (override markers use yellow/`--warning`, multiattack uses purple/`--accent`), per-item override highlighting (only tags individually modified saves/skills, not the whole array), `deepMerge` sparse object handling (base=array + override=non-array-object), `setNestedValue` array creation for numeric keys (root cause fix), `ensureArray()` safety net in `getTemplate()`, `reverseTurnStartEffects()` for Prev button condition re-increment, expired condition restore on Prev via `_expiredConditions` stash, ad-hoc combatant roll log for condition countdown notifications.
- **Phase 5.2** (done): Destructible ad-hoc combatants. Optional HP and AC fields on ad-hoc entries for destructible objects (doors, siege engines, gates, crystal pylons). When HP is set, gets full HP bar on row and Dmg/Heal/THP controls in detail panel (reuses existing `applyDamage`/`applyHeal`/`applyTempHp`). When AC is set, shows in AC column. Add Combatant modal updated with optional HP/AC inputs. `applyDamage`/`applyHeal` maxHp resolution updated: `c.hpMax || c.currentHp` fallback for non-template combatants. Fully backward compatible - ad-hoc entries without HP/AC behave identically to before.
- **Phase 5.3** (done): General-purpose dice roller. "Roll Dice" button centered in toolbar (dual spacers) opens a modal with flexible expression input. Recursive descent parser with operator precedence: supports multi-group (`100d100+100d100`), basic math (`2d8+8/2`, `2d8*2`), parentheses (`((2d8+8)+(2d8+8))/2`), shorthand (`d20` = `1d20`), advantage/disadvantage suffix (`1d20+5 adv`), and whitespace insensitivity (`1 d 20 + 3` = `1d20+3`). Grammar: `expression = term ((+|-) term)*`, `term = factor ((*|/) factor)*`, `factor = '(' expression ')' | NUMBER? 'd' NUMBER | NUMBER`. Display shows individual rolls for 2-10 dice, sum-in-parens for 11+, and collapses pure-number arithmetic. Division floors (D&D convention). Safety cap 1000 dice per group. History persists across modal close/reopen (ephemeral per session, capped at 50). New functions: `parseDiceExpression()`, `evalDiceNode()`, `rollDiceExpression()`, `showDiceRoller()`, `doDiceRoll()`, `renderDiceRollerResults()`. Does NOT modify existing `rollDice()` (combat rolls unchanged).
- **Phase 6** (done): Refactor - code quality pass with zero user-facing behavior changes. 6.1: `getCombatant(idx)` null-safe helper + guards on all 29 unsafe access sites. 6.2: Unified dice engines - old regex `rollDice()` replaced with thin wrapper over recursive descent parser, `evalDiceNode()` now propagates raw `rolls` array for crit detection. 6.3: All ~220 `var` declarations converted to `const`/`let`. 6.4: Global state consolidated - 8 edit-mode vars into `editState` object, 3 form vars into `formState` object. 6.5: Render helpers extracted - `getCombatantHp()`, `renderHpBar()`, `createModal()`. 6.6: Mega-functions broken up - `renderActiveCombat()` into `renderCombatBar()`/`renderCombatInfo()`/`renderCombatantRow()`, `renderCombatantDetail()` into `renderMonsterDetail()`/`renderPlayerDetail()`/`renderAdhocDetail()`. 6.7: CSS utility classes (`.flex-row`, `.flex-col`, `.text-muted-sm`, etc.) replacing 22 inline style patterns.
- **Phase 7** (done): 5etools monster importer. Complete import pipeline: `strip5eToolsTags()` (20+ tag types, nested, display text support for 3-segment reference tags), `flattenEntries()` (recursive structured entries with `item`/`itemSub` type handling), field parsers (size, type, AC, speed, saves, skills, CR, resistances, alignment with axis range analysis, initiative), `parse5eActions()` (attack/multiattack/feature splitting with hit bonus, reach/range, damage extraction), `parse5eLegendary()` (cost extraction with optional "Costs" prefix, continuation merging), `parse5eSpellcasting()` (at-will/daily/slots/recharge to features), `extractLegendaryResistances()` from traits, `import5etools()` orchestrator, `detect5eToolsFormat()`. Import modal uses toggle group (SquishText | 5etools) matching Add Combatant UX pattern. Format auto-detection in SquishText mode. Name-based dedup for external imports. Danger-styled import disclaimer warning DMs to review imported creatures. Bug fixes: hover duplication in speed, `|source` tag stripping in condition immunities, reach+range in attack notes, legendary continuation merging, recharge `usesMax: 1`, detail panel column balance (`minmax(0, 320px)`), legendary actions/resistances moved to left column, `flattenEntries` type-specific check ordering, senses tag stripping, display text for reference tags. Tested across 11 creatures covering all major categories (basic, legendary, villain actions, epic actions, spellcaster, mythic, swarm, lycanthrope, dragon, fungus). Edge cases (choice damage, versatile weapons, variable LA costs, mythic actions) covered by import disclaimer.
- **Phase 8** (done): Export enhancements - markdown export (full stat block to clipboard), image export (canvas-rendered PNG download). Both include all template fields.
- **Phase 9** (done): Attack descriptions (`desc` field on attacks for special effects text, inline clickable dice) and lair actions (`lairActions[]` array, reference-only display in combat, notification modal on combat start). 5etools importer updated for both.
- **Phase 10** (done): Template schema enhancements - `source` and `gear` fields on templates, displayed in list/combat/export. Legendary action conditional gate removed (no longer requires Actions/Round to add actions). Card styling on saves/skills/features/legendary/lair sections. Storage indicator fixed (measures app data, not origin-wide). Party input refocus. Export filenames include CR and source.
- **Phase 11** (done): Getting Started modal - onboarding for new users. Auto-shows on empty state, reopenable from footer link. 2x2 card grid (Monsters/Parties/Encounters/Combat) with click-to-detail views. "Load Sample Data" button (empty state only) imports embedded squishtext payload (ambush-at-thornhollow) via existing merge logic; stays on current tab after import. Docs updated: INDEX.md example data section, IMPORT_EXPORT.md export filename, MONSTER_FORMAT.md complete importable example with export wrapper.
- **Phase 12+** (future): CritterDB, Bestiary Builder importers (each in own phase). See `PLAN.md` for details.

## Development Notes

- Ability score inputs use inline DOM updates (`document.getElementById('mod_X').textContent = ...`) to preserve tab order - a full re-render would steal focus
- Feature and legendary action sections show column headers (`dyn-header`) only when rows exist
- Attack damage rows are nested inside attack cards with a left border indent
- Roll log uses `renderRollLog()` for live DOM updates without full re-render when rolling
- Condition form uses `updateConditionFields()` for dynamic field switching based on duration type dropdown. Custom condition uses `toggleCustomCondInput()` for inline text input (no browser `prompt()`).
- **Add Combatant modal**: `showAddCombatantModal()` renders a modal with three modes (Ad-hoc / Player / Monster). `switchAddMode()` toggles between them. `doAddCombatant()` handles creation - ad-hoc (name + init), player (name, init, AC, notes), or template monster with retroactive numbering. Monster picker uses `filterCombatMonsterPicker()` / `selectCombatMonster()` with separate DOM IDs (`combatMonsterPicker` / `combatMonsterPickerDropdown`) to avoid collision with the encounter form picker.
- **`createMonsterCombatant(template, name)`**: Shared helper extracted from `launchCombat()`. Creates a full monster combatant instance with rolled initiative and HP from `template.hpMax`. Used by both `launchCombat()` and `doAddCombatant()`.
- **Retroactive numbering**: `getExistingMonsterCount()` counts existing combatants for a template. When adding more of the same, bare-named instances (e.g., "Goblin") get renamed to "Goblin 1" before new ones start at the next number.
- `applyTurnStartEffects()` is the single consolidated turn-start hook - all turn-start additions go here, not in `nextTurn()` or `editInit()` directly
- Recharge rolls are DM-initiated (notification + button), NOT auto-rolled on turn start
- **Button classes**: Use `.btn-accent`, `.btn-success`, `.btn-warning`, `.btn-danger` for colored buttons - never inline `style="color:var(--accent)"` as it breaks hover contrast. Each class has proper `:hover` with `color: white !important`. `.btn-remove` is the global class for × delete/remove buttons (red border, red text, red bg on hover).
- **Color scheme**: Override markers (highlight, badge, name, edit-field-modified, revert button) use yellow (`--warning`). Multiattack box uses purple (`--accent`). Proficient saves/skills use accent color. This ensures overrides are visually distinct from structural elements.
- **Multi-combat**: All combat functions use `getActiveCombat()` instead of `state.combat`. `activeCombatId` is persisted in `state.preferences` (restored on load). `saveCombat()` saves the entire `state.combats` array plus `activeCombatId` and `autoExpandedId` to preferences. Migration from old single-combat format (`pf_enc_combat`) is automatic in `load()`.
- **Searchable monster picker**: Encounter form uses `filterMonsterPicker()` + `selectMonster()` with a `.search-select` dropdown. Monster ID stored in `data-template-id` on the input element.
- **Import/Export**: Uses embedded SquishText-compatible compression (deflate-raw + CRC32). `.squishtext` file extension. SquishText format only (no JSON fallback). Smart merge on import - skips duplicates by ID, keeps existing data, adds new items only.
- **Save Backup / Load Backup**: Full data backup/restore. Load Backup uses direct file picker (no modal). Save Backup downloads `.squishtext` file.
- **Import Monster / Export Monster**: Per-monster `.squishtext` files. Export button on each monster card. Import Monster button in toolbar (visible on Monsters tab only). Import modal has toggle group (SquishText | 5etools). SquishText mode: file picker for `.squishtext` files, dedupes by ID, auto-detects 5etools JSON format and routes to correct parser. 5etools mode: paste textarea + file picker for `.json`, dedupes by name, danger-styled warning about data variability. `switchImportMode(mode)` toggles body content.
- **5etools importer**: Pure function `import5etools(json) → template[]`. Detects single creature, array, or `{monster:[]}` wrapper. Tag stripping via `strip5eToolsTags()` (loops until no `{@` remains for nested tags; reference tags with 3 segments use display text, e.g., `{@variantrule Cone [Area of Effect]|XPHB|Cone}` -> "Cone"). Entry flattening via `flattenEntries()` (handles strings, `type:"item"`, `type:"itemSub"`, `type:"list"`, `type:"table"`, nested `entries`; type-specific checks (`item`/`itemSub` with `name`) run before generic `entries.entries` fallback; supports `entry` singular field). Action parsing splits attacks from features via `{@atkr}`/`{@atk}`/`{@hit}` tag detection. Legendary action parser merges continuation entries (entries without `Action N:` or `(Costs N Actions)` prefix); cost regex has optional "Costs" prefix. `parse5eAlignment()` uses axis analysis for alignment ranges (detects law/chaos and good/evil axes to produce "Any Evil", "Any Alignment", etc.). `extractRechargeFromName()` handles `{@recharge N}`, `(Recharge N-6)`, `(N/Day)`, `(Recharges after rest)` - recharge abilities get `usesMax: 1`. Senses array joined and wrapped with `strip5eToolsTags()`. External imports get new `uuid()` IDs. Danger-styled import disclaimer on modal warns DMs to review imported creatures. See `PLAN.md` Phase 7 for field mapping details. See `5ETOOLS_CREATURE_SCHEMA.md` (in the tools repo at `references/5ETOOLS_CREATURE_SCHEMA.md`) for the full schema reference.
- **Combat override system**: `deepMerge(base, overrides)` recursively merges overrides onto templates. Same-length arrays use index-based merge (attacks/features/LAs - index-referenced, cannot add/remove). Different-length arrays use full replacement (saves/skills - name-referenced, can add/remove). Handles sparse object overrides (when base is array but override serialized as `{"0": {...}}` object with string numeric keys). `sparseOverrides(base, edited)` produces minimal delta. `getTemplate(c)` returns merged result with `ensureArray()` safety net on all array fields (savingThrows, skills, attacks, features, legendaryActions) to guard against corrupted sparse-object overrides. `getRawTemplate(c)` returns raw template for edit form diffing. `isFieldOverridden(c, fieldPath)` navigates overrides by dot-path for visual highlighting; per-item save/skill highlighting compares individual values against `getRawTemplate()` rather than checking the whole array. `setNestedValue()` creates arrays (not objects) when the next path segment is numeric, preventing sparse object corruption at the source. Edit mode uses `editState` object (`.mode`, `.selected`, `.lockedTemplateId`, `.formData`, `.visible`, `.rawTemplate`, `.conflicts`, `.formSnapshot`) - all cleared on exit or view switch.
- **Edit mode controls**: When `editState.mode` is active, combat controls (Prev/Next/+Add/End Combat/Back to List) are disabled. Only Edit Mode toggle and Edit Selected remain active. Checkboxes appear on monster rows; first selection locks `editState.lockedTemplateId`, non-matching templates are dimmed/disabled. Edit form replaces combatant rows when `editState.visible` is true. Conflicting fields (different values across batch-selected combatants) show italic "Varies" placeholder, are disabled, and dimmed (`.edit-field-conflict`). `editVal(val, fieldPath)` helper clears values for conflicted fields so the placeholder is visible. `editFieldAttr(fieldPath)` adds the disabled/placeholder/class attributes inline.
- **Per-item save/skill revert**: Saves match by ability name, skills match by skill name. `isSaveItemModified(idx)` / `isSkillItemModified(idx)` return true for modified or added items. Revert restores template values for modified items, splices out added items. Deleted items (in template but not in edit form) shown struck-through with restore button. Bonus input and dropdown changes trigger re-render for immediate visual feedback.
- **Roll mode popup**: Single `#rollModePopup` div shared by all roll buttons, positioned via `positionPopup()` with viewport edge clamping (8px margin). Buttons stack vertically (`flex-direction: column`). `showRollPopup(evt, fnName, cIdx, param)` renders Dis/Normal/Adv for d20 rolls, `showDmgPopup(evt, cIdx, atkIdx)` renders Normal/Crit for damage. `fireRollPopup(mode)` calls the stored function via `window[fnName]()`. `fireDmgPopup(crit)` calls `rollDamageForAttack()`. Click outside dismisses via `document.addEventListener('click', hideRollPopup)`. Popup content rendered dynamically (innerHTML) on each open.
- No backwards compatibility concerns yet - tool is pre-release
- Context: heroic/homebrew D&D - CR100 monsters, level 60 players, non-standard rules are expected
