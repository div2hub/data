# CSV Data Conventions

These CSVs are the **source of truth** for all Division 2 game data used by tools.

## Attribute Column Value Syntax

Used in `core_1`, `core_2`, `core_3`, `minor_1`, `minor_2`, `minor_3` columns across all gear and weapon CSVs.

| Syntax | Meaning | Example |
|--------|---------|---------|
| `type:<slug>` | Selectable attribute. `<slug>` matches the `compatibility` column in `attributes.csv` | `type:gear-core`, `type:weapon-minor` |
| `type:<slug>\|!Stat1\|!Stat2` | Selectable attribute with exclusions — any compatible attribute except the listed stats | `type:gear-minor\|!Headshot Damage\|!Health\|!Repair Skills` |
| `fixed:<name>` | Fixed to that stat, value rolls within min/max range from `attributes.csv`. `<name>` must match an attribute name exactly | `fixed:Assault Rifle Damage` |
| `fixed:<name>:<value>` | Fixed to that exact stat and value | `fixed:Headshot Damage:20%` |
| `N/A` | This slot does not exist on this piece | |

**Important:** Blank cells are not allowed. Every attribute slot must use `type:`, `fixed:`, or `N/A`.

### Attribute Compatibility Slugs

The `compatibility` column in `attributes.csv` uses pipe-delimited slugs that match `type:` references in gear/weapon CSVs:

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
| `type:<type>` | Selectable mod slot. `<type>` maps to `compatible_types` in `weapons/weapon_mods.csv` | `type:short`, `type:5.56` |
| `fixed:<name>` | Fixed mod, not selectable. `<name>` must match a name in `weapons/weapon_mods.csv` | `fixed:Osprey .45 Suppressor` |
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

- `is_named` and `is_exotic` use uppercase `TRUE`/`FALSE`

## Empty Cells

Empty cells are **not allowed** in any CSV. Every cell must contain a value or `N/A` (meaning "this field does not apply to this item"). Empty cells that are not registered in `known_gaps.json` are a data error.

## Known Gaps (`known_gaps.json`)

When data is legitimately unavailable (e.g., unreleased content, stats that require in-game verification), register the gap in `known_gaps.json` instead of leaving unexplained empty cells.

Each entry has:
- `reason` — why the data is missing
- `expires` — ISO date after which the gap becomes a build error
- `gaps` — list of `{ file, name, columns }` specifying exactly which cells are empty

The `name` field matches the row's `name` (or `stat_name`) column. Use `@all` to match all rows in a file for a given column.

**Rules:**
- Matched + not expired → warning (build passes)
- Matched + expired → error (build fails — fill the data or extend the deadline)
- Unmatched → error (build fails — unknown empty cell)

When data becomes available, fill the CSV cells and remove the corresponding entry from `known_gaps.json`.

## No Comments in CSVs

CSVs must not contain comment lines (e.g., `# NOTE:`). All documentation belongs in this README.

## Per-Slot Gear CSVs

Files: `gear/masks.csv`, `gear/backpacks.csv`, `gear/chests.csv`, `gear/gloves.csv`, `gear/holsters.csv`, `gear/knees.csv`

Each row is a gear piece (generic brand, generic gear set, named, or exotic). The `brand_set` or `gear_set` column links to `gear/brand_sets.csv` or `gear/gear_sets.csv` for set bonuses. The unused link column (`gear_set` on brand pieces, `brand_set` on gear set/exotic pieces) must be `N/A`.

- **Generic brand piece**: `brand_set` filled, `gear_set=N/A`, `name` is `{Brand} {Slot}` (e.g., "5.11 Tactical Mask")
- **Named with talent**: `brand_set` filled, `is_named=true`, `fixed_talent` filled
- **Named with attribute override**: `brand_set` filled, `is_named=true`, fixed minor column(s) filled
- **Gear set piece**: `gear_set` filled, `brand_set=N/A`, `minor_2=N/A` (gear sets have 1 minor, not 2)
- **Exotic**: `is_exotic=true`, `brand_set=N/A`, `gear_set=N/A`, all attributes typically fixed

## Specialization CSVs

Two file groups:

- One per-spec CSV per specialization at `specializations/{spec}.csv` — tree shape (parents), per-tier costs, hub budgets. Specialization is implied by filename; no `specialization` column.
- One shared `specializations/specialization_talents.csv` — one row per talent variant. For tiered nodes whose `category` is `talent`, that's one row per `{name} Tier {N}`. Holds per-tier descriptions with concrete values.

### Per-spec CSV columns

| Column | Sub-hub | Stat node |
|---|---|---|
| `name` | unique display name within the spec | unique display name within the spec |
| `type` | `hub` | `node` |
| `category` | `N/A` | `talent` or `item` (see below) |
| `parent` | `N/A` for spec-center children, otherwise another row's `name` in this file | same |
| `description` | `N/A` | for `category=item`: the item's description (per-spec CSV is the source of truth). For `category=talent`: `N/A` — descriptions live in `specialization_talents.csv` (one per tier). |
| `budget` | sub-hub's point cap | `N/A` |
| `max_tier` | `N/A` | `1`–`5` |
| `tierN_cost` (`N=1..5`) | `N/A` | point cost to buy tier `N`, filled for `N <= max_tier`, `N/A` otherwise |

#### `category` values

- `talent` — node activates an in-game talent. Per-tier prose with concrete values lives in `specialization_talents.csv` (one row per `{name} Tier {N}`).
- `item` — node unlocks an in-game item (grenade, sidearm, signature weapon, skill mod). One description, no per-tier prose.

### `specialization_talents.csv` columns

One row per talent variant; tiered talents have one row per `{name} Tier {N}` for `N=1..max_tier`. Spec talents are not selectable (they're activated by spending points in the per-spec tree), so there's no `compatibility` column.

| Column | Meaning |
|---|---|
| `name` | display name including tier suffix (e.g. `This is my Rifle Tier 1`). Where the same in-game talent name appears in multiple specs with different effects (e.g. Signature Ammo Acquisition), disambiguate with a `(Spec)` qualifier between name and tier suffix (e.g. `Signature Ammo Acquisition (Sharpshooter) Tier 1`). |
| `description` | per-tier prose with concrete values (e.g. `Increases Rifle Damage by 5%`) |

### Rules

- Sub-hubs can be children of the spec center or of stat nodes; sub-hubs cannot be parents of other sub-hubs (no nested hubs).
- The spec center is implicit — no row in any CSV.
- The spec center's global point cap (165 in-game) is not represented; only sub-hub budgets are.
- For `category=talent` nodes, every tier `N` in `1..max_tier` must have a matching `{name} Tier {N}` row in `specialization_talents.csv`.
- `type ∈ {hub, node}`; `category ∈ {talent, item}` for nodes, `N/A` for hubs.
- Node rows must have `max_tier ∈ 1..5`, exactly the first `max_tier` `tierN_cost` columns filled and the rest `N/A`. `description` is filled for `category=item` rows and `N/A` for `category=talent` rows (descriptions for talents live in `specialization_talents.csv`).
- Hub rows must have `budget` set, with `category`, `description`, `max_tier`, and all `tierN_cost` set to `N/A`.
- `parent` resolves to another row's `name` in the same file or is `N/A`.
- Node names are unique within a file.
