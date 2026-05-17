# My Modifications — Mod Conventions

## What the mod does

My Modifications is a personal grab-bag mod — not a single themed overhaul but a long-running collection of experiments, character event chains, custom ideologies, parties, government types, modifiers and quality-of-life tweaks. It is the lab where ideas that aren't big enough for their own repo live and accumulate.

The currently load-bearing content blocks are:

- Character event chains around real and fictional figures: **Corleone** (the Gangs of New York journal entry, Vito → Michael handoff), **Stoker** (the Dmitry chain), **Lovecraft**, **Milei**, **Nietzsche**.
- A monthly **anarchist balkanisation** mechanic that spins radicalised non-capital states of low-legitimacy anarchist countries off as dynamic countries.
- A passive **global religion tracker** journal entry, computed once per month in `on_actions` rather than per-country.
- A set of custom **character ideologies** (`anarcho_libertarian`, `anti_feminist`, `anti_statist`, `folkist`, `libertarian_utopian`, `secular_humanitarian`) and a matching `anarcho_libertarian_party`.
- Custom **regency** government types (`gov_regency_mym_military`, `gov_regency_mym_noble`).
- A **debug menu** (`decision_mym_debug_menu` plus events in the `mym_debug` namespace) for ad-hoc state surgery.
- Smaller decisions: `decision_want_to_be_free`, `decision_mym_enact_the_act`, `decision_mym_force_privatization`, plus the `heads_or_tails` chain.

Tone varies by chain. The character events are written as in-universe newspaper clippings (#italic dateline + #! body), the gangster chain is pulp-noir, Stoker is *Dracula*-pastiche captain's-log, Lovecraft is *Weird Tales* pastiche, anarchist balkanisation is dry and mechanical. Don't homogenise the voice — each chain owns its register.

## Mod abbreviation

Internally the mod is **MYM** (My Modifications). Use `mym_` / `MYM` prefixes consistently in new files and SSM-style identifier slots as documented below.

## File and identifier prefixing

All script and localization files carry the `mym_` prefix without exception:

- `common/<category>/mym_*.txt`
- `events/**/mym_*.txt` (character chains live under `events/character_events/`)
- `localization/english/mym_*_l_english.yml`
- `dlc_metadata/mym_dlc_metadata.txt`

This is a hard rule. Vanilla overrides must still live in `mym_`-prefixed files so the mod's footprint is greppable.

In-game definitions (decisions, journal entries, government types, parties, ideologies) generally do **not** carry the `mym_` prefix when they belong to vanilla-style identifier families — but they may carry it when the bare name is too generic. Examples:

- No prefix: `je_gangs_of_new_york`, `decision_want_to_be_free`, `vito_corleone_character_template`, `michael_corleone_character_template`.
- With `mym_` infix/prefix where ambiguity would otherwise bite: `je_mym_global_religion_tracker`, `decision_mym_debug_menu`, `decision_mym_enact_the_act`, `decision_mym_force_privatization`, `gov_regency_mym_military`, `gov_regency_mym_noble`.

When in doubt, prefer the bare vanilla-style identifier and switch to `mym_`-infix only if a collision is plausible.

Exceptions where `mym_` *is* used consistently in identifiers:

- Scripted triggers that scope geography / religion / ideology by MYM-specific logic and could collide with vanilla wording: `mym_state_is_in_europe`, `mym_country_is_in_south_america`, `mym_is_jewish`, `mym_is_christian`, etc.
- On-action handlers and per-mod hook effects: `on_monthly_pulse_country_mym_random_character_events`, `on_monthly_pulse_country_mym_character_events`, `on_monthly_pulse_country_mym_events`, `on_monthly_pulse_country_mym`.
- DLC metadata key: `modmym`.
- Debug events namespace: `mym_debug`.

## Event namespaces

Default rule: use the **theme or proper-noun** as the namespace, no `mym_` prefix.

Examples currently in the codebase: `corleone`, `hp_lovecraft`, `milei`, `nietzsche`, `stoker`, `anarchist_balkanisation`, `heads_or_tails`.

Add the `mym_` prefix to the namespace only when the bare name would collide with vanilla or with a different mod, or is too generic to stand alone. That is why `mym_debug` is prefixed today — `debug` alone is the kind of word a dozen mods would claim.

## Localization key suffixes for event heads

Use these suffixes for the three head fields of every event localization entry:

| Field   | Suffix | Example                |
| ------- | ------ | ---------------------- |
| title   | `.tt`  | `corleone.1.tt`        |
| desc    | `.dd`  | `corleone.1.dd`        |
| flavor  | `.ff`  | `corleone.1.ff`        |

This is a project-wide convention across my mods. Do not standardize to vanilla `.t` / `.d` / `.f`.

Note: a handful of older event entries in this mod (e.g. `corleone.9.t/d/f`, some `stoker` entries) still use the vanilla single-letter suffixes. These are legacy. New events MUST use `.tt` / `.dd` / `.ff`; when touching an old event in this mod, migrate its head keys to the new suffix while you're there.

Option keys keep single-letter suffixes (`.a`, `.b`, `.c`, ..., `.aa` for "default close" options where used).
Custom tooltip keys keep the `_tt` suffix.

## Variable naming

Mandatory suffixes for stored variables:

| Scope                                              | Suffix      | Example                                       |
| -------------------------------------------------- | ----------- | --------------------------------------------- |
| Scope variables (country, state, character, …)     | `_var`      | `heads_or_tails_var`, `heads_character_var`, `tails_character_var` |
| Global variables (`set_global_variable`)           | `_globvar`  | `vito_corleone_happened_globvar`, `michael_corleone_happened_globvar`, `lovecraft_happened_globvar`, `milei_happened_globvar`, `vlad_happened_globvar` |

Flags are not used in this mod. Prefer variables over flags for new logic so the naming convention stays unambiguous. `set_global_variable` is the standard signal for "this historical character / one-off event has already happened in this run" — keep that idiom.

## Custom tooltip suffix

Custom tooltips and `custom_tooltip = { text = … }` keys end in `_tt`, e.g. `mym_debug.1.k_tt`.

## Character templates

Character templates use the `_character_template` suffix (`vito_corleone_character_template`, `michael_corleone_character_template`, `hp_liebkraft_character_template`, ...). Their `on_created` block sets the corresponding `*_happened_globvar` so journal-entry completion and downstream events can branch on whether the character ever spawned.

## Localization style

Localization varies by chain. The character event chains are written as in-universe **newspaper clippings** with the `#italic <City - dateline>#!#!` opener and `#bold <Subheadline>#!` for breaking news, generous use of `$concept_*$` / scope-variable token expansion, and Paradox text-format tags (`#bold`, `#italic`, `#lore`, `#red`, `#!`). The Stoker chain mimics a ship's log; Lovecraft mimics a weird-fiction magazine. Match the chain's voice when extending it; don't sand it down.

Code comments and commit messages remain English-only (see project-level instruction).

## Content gating

This mod is not gated behind a game rule. New chains are added with their own `is_shown` / `possible` / `trigger` filters and the assumption that anyone running MYM wants the lot.

The debug menu is gated only by `decision_mym_debug_menu` being visible — there is no separate `mym_debug_menu_enabled` rule. If exposure of the debug menu becomes a concern (e.g. shipping a Steam build), introduce a `mym_debug_menu` game rule and gate the decision behind it, following Solism's pattern.

## Asset pipeline

Binary assets (`*.dds`, `*.png`, `*.tga`, `*.ttf`, `*.otf`, `*.ogg`, `*.bank`) are tracked via Git LFS — see `.gitattributes`. Don't commit large binaries without LFS.

`.gitignore` excludes vanilla mirror folders kept locally for reference (`dlc/`, `soundtrack/`, `fonts/`, `map_data/`) plus scratch artifacts (`changes.txt`, `localizations_*.txt`, `scripts_*.txt`, `diff.txt`). Keep new throwaway tooling output matching those patterns.

## CHANGELOG / README

Per project instruction, CHANGELOG and README entries describe *what the player notices*, not implementation details. Because this mod is a grab-bag, group new entries by feature area (character chains / decisions / ideologies / QoL) rather than by file or system. The README's framing — "personal changes and experiments … not about a clear goal" — is the authoritative scope statement; don't over-promise coherence the mod doesn't claim.
