# CSV Data Conventions

These CSVs are the **source of truth** for all Division 2 game data used by tools.

## Attribute Column Value Syntax

Used in `core_1`, `core_2`, `core_3`, `minor_1`, `minor_2`, `minor_3` columns across all gear and weapon CSVs.

| Syntax | Meaning | Example |
|--------|---------|---------|
| `type:<slug>` | Selectable attribute. `<slug>` matches the `compatibility` column in `attribute_rolls.csv` | `type:gear-core`, `type:weapon-minor` |
| `type:<slug>\|!Stat1\|!Stat2` | Selectable attribute with exclusions — any compatible attribute except the listed stats | `type:gear-minor\|!Headshot Damage\|!Health\|!Repair Skills` |
| `fixed:<name>` | Fixed to that stat, value rolls within min/max range from `attribute_rolls.csv`. `<name>` must match an attribute name exactly | `fixed:Assault Rifle Damage` |
| `fixed:<name>:<value>` | Fixed to that exact stat and value | `fixed:Headshot Damage:20%` |
| `N/A` | This slot does not exist on this piece | |

**Important:** Blank cells are not allowed. Every attribute slot must use `type:`, `fixed:`, or `N/A`.

### Attribute Compatibility Slugs

The `compatibility` column in `attribute_rolls.csv` uses pipe-delimited slugs that match `type:` references in gear/weapon CSVs:

| Slug | Used by |
|------|---------|
| `gear-core` | All gear core slots (`type:gear-core`) |
| `gear-offensive-core` | Offensive-only core restriction |
| `gear-defensive-core` | Defensive-only core restriction |
| `gear-skill-core` | Skill-only core restriction |
| `gear-minor` | All gear minor slots (`type:gear-minor`) |
| `gear-offensive-minor` | Offensive-only minor restriction (e.g., Acosta's Kneepads) |
| `gear-defensive-minor` | Defensive-only minor restriction (e.g., Acosta's Kneepads) |
| `gear-skill-minor` | Skill-only minor restriction |
| `weapon-minor` | All weapon minor slots (`type:weapon-minor`) |
| `weapon-offensive-minor` | Offensive-only weapon minor restriction |
| `weapon-skill-minor` | Skill-only weapon minor restriction |
| `N/A` | Non-selectable attribute (weapon cores, burn damage, etc.) |

Each selectable attribute has both a broad slug (e.g., `gear-minor`) and a specific slug (e.g., `gear-offensive-minor`). A `type:gear-minor` slot matches all attributes with `gear-minor` in their compatibility. A `type:gear-offensive-minor` slot only matches the subset with that specific slug.

### Core Attribute Naming

All weapon core attributes use a `(Core)` qualifier in their name for consistency. This distinguishes them from selectable minor attributes (some of which share the same base name with different roll ranges):

| Core name in CSV | Display name |
|------------------|-------------|
| `Weapon Damage (Core)` | Weapon Damage |
| `Assault Rifle Damage (Core)` | Assault Rifle Damage |
| `LMG Damage (Core)` | LMG Damage |
| `SMG Damage (Core)` | SMG Damage |
| `Rifle Damage (Core)` | Rifle Damage |
| `Marksman Rifle Damage (Core)` | Marksman Rifle Damage |
| `Shotgun Damage (Core)` | Shotgun Damage |
| `Pistol Damage (Core)` | Pistol Damage |
| `Health Damage (Core)` | Health Damage |
| `DTOC (Core)` | DTOC |
| `Critical Hit Chance (Core)` | Critical Hit Chance |
| `Damage to Armor (Core)` | Damage to Armor |
| `Critical Hit Damage (Core)` | Critical Hit Damage |
| `Headshot Damage (Core)` | Headshot Damage |

The `(Core)` qualifier is stripped by `display-names.ts` before rendering. Weapon CSVs reference these as `fixed:Assault Rifle Damage (Core)`, etc.

## Delimiter

- `|` (pipe) separates multiple values within a single column (e.g., brand set bonuses, weapon restrictions, exclusion lists)

## Stat Format

- Stats use `Stat Name:Value` format (e.g., `Critical Hit Damage:15%`)
- Percentage values include `%` suffix
- Flat values are bare numbers (e.g., `Armor Regen:4925`)
- Some attributes can be *either* a percent or a flat value

## Weapon Mod Column Syntax

Used in `optics`, `magazine`, `muzzle`, `underbarrel` columns across all weapon CSVs.

| Syntax | Meaning | Example |
|--------|---------|---------|
| `type:<type>` | Selectable mod slot. `<type>` maps to `compatible_types` in `weapon_mods.csv` | `type:short`, `type:5.56` |
| `fixed:<name>` | Fixed mod, not selectable. `<name>` must match a name in `weapon_mods.csv` | `fixed:Osprey .45 Suppressor` |
| `N/A` | Mod slot not available on this weapon | |
| *(empty)* | Missing data — needs to be filled in | |

## Weapon Mods Compatible Types (`weapon_mods.csv`)

| Syntax | Meaning |
|--------|---------|
| `@any` | Mod is compatible with all slot types |
| `short\|long` | Mod is compatible with listed types (pipe-separated) |
| *(empty)* | Missing data — needs to be filled in |

## Weapon Slot Type

The `slot_type` column in weapon CSVs indicates which equipment slots the weapon can occupy:

| Value | Meaning |
|-------|---------|
| `main` | Can be equipped in Primary or Secondary slot |
| `sidearm` | Can only be equipped in the Sidearm slot |

All pistols are `sidearm`. Sawed-Off family shotguns are `sidearm`. Everything else is `main`.

## Boolean Columns

- `is_named` and `is_exotic` use lowercase `true`/`false`

## No Comments in CSVs

CSVs must not contain comment lines (e.g., `# NOTE:`). All documentation belongs in this README.

## Per-Slot Gear CSVs

Files: `masks.csv`, `backpacks.csv`, `chests.csv`, `gloves.csv`, `holsters.csv`, `knees.csv`

Each row is a gear piece (generic brand, generic gear set, named, or exotic). The `brand_set` or `gear_set` column links to `brand_sets.csv` or `gear_sets.csv` for set bonuses.

- **Generic brand piece**: `brand_set` filled, attributes use `type:` slugs (user configurable), name prefixed with `TODO_` until real in-game name is provided
- **Named with talent**: `brand_set` filled, `is_named=true`, `fixed_talent` filled
- **Named with attribute override**: `brand_set` filled, `is_named=true`, fixed minor column(s) filled
- **Gear set piece**: `gear_set` filled, `minor_2=N/A` (gear sets have 1 minor, not 2)
- **Exotic**: `is_exotic=true`, no brand/gear set, all attributes typically fixed
