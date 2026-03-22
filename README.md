# CSV Data Conventions

These CSVs are the **source of truth** for all Division 2 game data used by tools.

## Attribute Column Value Syntax

Used in `core_1`, `core_2`, `core_3`, `minor_1`, `minor_2`, `minor_3` columns across all per-slot gear CSVs.

| Syntax | Meaning | Example |
|--------|---------|---------|
| *(empty)* | User can pick any valid attribute | |
| `Stat:Value` | Fixed to that exact stat and value | `Headshot Damage:20%` |
| `Stat` | Fixed to that stat, value rolls within min/max range from `attributes.csv` | `Status Effects` |
| `N/A` | This slot does not exist on this piece | |
| `Red` | Any Red (offensive/weapon damage) attribute | |
| `Blue` | Any Blue (defensive/armor) attribute | |
| `Yellow` | Any Yellow (utility/skill tier) attribute | |
| `!Stat1\|!Stat2` | Any attribute EXCEPT the listed stats | `!Headshot Damage\|!Health\|!Repair Skills` |

## Delimiter

- `|` (pipe) separates multiple values within a single column (e.g., brand set bonuses, weapon restrictions, exclusion lists)

## Stat Format

- Stats use `Stat Name:Value` format (e.g., `Critical Hit Damage:15%`)
- Percentage values include `%` suffix
- Flat values are bare numbers (e.g., `Armor Regen:4925`)
- Some attributes can be *either* a percent or a flat value

## Boolean Columns

- `is_named` and `is_exotic` use lowercase `true`/`false`

## Per-Slot Gear CSVs

Files: `masks.csv`, `backpacks.csv`, `chests.csv`, `gloves.csv`, `holsters.csv`, `knees.csv`

Each row is a gear piece (generic brand, generic gear set, named, or exotic). The `brand_set` or `gear_set` column links to `brand_sets.csv` or `gear_sets.csv` for set bonuses.

- **Generic brand piece**: `brand_set` filled, attributes empty (user configurable), name prefixed with `TODO_` until real in-game name is provided
- **Named with talent**: `brand_set` filled, `is_named=true`, `fixed_talent` filled
- **Named with attribute override**: `brand_set` filled, `is_named=true`, fixed minor column(s) filled
- **Gear set piece**: `gear_set` filled, `minor_2=N/A` (gear sets have 1 minor, not 2)
- **Exotic**: `is_exotic=true`, no brand/gear set, all attributes typically fixed
