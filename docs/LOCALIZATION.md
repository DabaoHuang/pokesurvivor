# Traditional Chinese localization

The `zh-Hant` option extends the existing Spanish-source translation system. Choose
**Settings → Graphics → Language → 繁體中文**. The choice is stored in `ps.lang` and
reloads the game, like the existing language options.

## Language selection

A supported saved preference wins, followed by the existing `/en/` URL override,
then the first browser language. `zh-Hant` (including regional subtags), `zh-TW`,
`zh-HK` and `zh-MO` select Traditional Chinese. Spanish browser languages select
Spanish; other languages retain the existing English fallback. The early loading
screen and runtime use the same rules. This does not add a Simplified Chinese
locale or a separate Chinese website route.

## Files and data contracts

- `js/core/i18n.js`: selects the language and translates DOM text, attributes,
  Canvas text/measurement and browser alerts/confirmations.
- `js/data/lang-zh-hant.js`: UI, descriptions, personality quiz, missions,
  achievements, release notes and dynamic message templates.
- `js/data/lang-zh-hant-names.js`: species, move, item, type and region names.
- `G.I18n.add(dictionary, language = 'en', context = '')` loads only the active
  language. Existing English dictionary calls keep their default behavior.
- Spanish strings remain the keys. `{x}` translates its captured content;
  `{name}` preserves player-supplied nicknames. Keep placeholder order and count.
- `G.T(name, 'move')` resolves contextual move names before the general dictionary.
  Spanish **Psíquico** means both the Psychic type (**超能力**) and the move Psychic
  (**精神強念**); these must not share the same Chinese label.
- `translate="no"` continues to protect DOM nicknames. `rawCanvas` renders player
  names without treating names such as `Pikachu` as species names.
- IDs, dex numbers, game definitions, combat values, save keys and network payloads
  keep their original values. Translated `<option>` labels retain explicit source
  values when saving profile titles. Species search accepts both source and
  translated names.
- Chinese uses installed CJK fonts (PingFang TC, Microsoft JhengHei, Noto Sans CJK
  TC, then system fallback). No font service or runtime name API is added. Canvas
  drawing and measurement use the same fallback stack.

Untranslated future strings retain the original source text, as in the existing
translation system. External guide, Pokédex, support and contact pages keep their
existing Spanish/English content; this contribution localizes the playable game.
Historical release notes and legal text are translated as written, not rewritten
as a new description of the backend.

## Name provenance (checked 2026-10-13)

Baseline data is the existing project's data provider,
[PokéAPI](https://pokeapi.co/docs/v2), using Traditional Chinese (`zh-Hant`, language
ID 4) from revision
[`81d20919845b6a445969b9265aab5cec6b04cece`](https://github.com/PokeAPI/pokeapi/tree/81d20919845b6a445969b9265aab5cec6b04cece/data/v2/csv).
The relevant tables are `pokemon_species_names.csv`, `move_names.csv`,
`item_names.csv`, `type_names.csv` and `region_names.csv`.
PokéAPI is community-maintained, not a Pokémon Company publication.

All **966 species** present in this game were compared by national dex number
against the base-form names in the
[Taiwan official Pokédex](https://tw.portal-pokemon.com/play/pokedex/).
One discrepancy was found: **#983 Kingambit uses 仆斬將軍**, matching the official
Pokédex, instead of the downloaded table's 仆刀將軍.

Move translations follow the actual Spanish display name (with its existing
English translation as a cross-check), not blindly the internal identifier:

| Internal ID / source label | Displayed Traditional Chinese | Reason |
| --- | --- | --- |
| `leaf-storm` / Tormenta Floral | 落英繽紛 | The displayed move is Petal Blizzard. |
| `giga-drain` / Hierba Lazo | 打草結 | The displayed move is Grass Knot. |
| `psychic` / Psíquico | 精神強念 | Separate from the type name 超能力. |
| Enjambre / Swarm | 蟲群 | Project-specific evolved move, not an official move. |
| Mejora | 強化 | UI upgrade action, not the official item Upgrade. |
| Diario | 每日 | Leaderboard period, not the item Journal. |
| Niebla | 迷霧 | Weather label, not the move Haze. |

Official names are used for species, recognized moves and held items. Original
game mechanics, upgrade labels and custom arenas have descriptive translations;
their effects remain those of PokéSurvivor, not the main-series Pokémon games.

## Validation

Dependency-free translation and contract checks:

```sh
node --test tests/i18n.test.cjs
```

Browser integration checks use Playwright and an installed Google Chrome. If
needed, install Playwright locally without adding project dependencies:

```sh
npm install --no-save --package-lock=false playwright
node tests/i18n-browser.cjs dev.html
```

Alternatively install Playwright's Chromium and set `PLAYWRIGHT_CHANNEL=chromium`.
The test starts a loopback-only static server and a fresh browser profile,
disables Firebase in that profile, blocks external requests and service workers,
and writes screenshots/reports to `test-results/i18n` (or `I18N_TEST_OUTPUT`). It
never uses a personal browser profile or production accounts. Do not commit test
artifacts. Add `/test-results/` to `.git/info/exclude` for local runs.

The browser checks cover registration and selection, menus, species search,
profile title values, a local gacha draw, settings, persisted language changes,
unchanged saved progress across reloads, a run/pause/result, item and bond cards,
release notes, player-name preservation and an 844×390 viewport. Card progression
and leaderboard rendering use local fixtures; these are not end-to-end multiplayer
or production Firebase tests. Short gameplay checks do not establish game balance
or long-session reliability.

Build and regenerate the existing entry points according to `CLAUDE.md`:

```sh
node tools/build.js
node tools/export_data.js
python3 tools/make_seo_pages.py
node tests/i18n-browser.cjs index.html
node tests/i18n-browser.cjs en/index.html
```

The SEO generator may normalize CRLF files to LF on macOS. Preserve the existing
line endings before submitting so unrelated pages are not part of the PR. Keep
the regenerated `index.html`, `en/index.html`, `js/app.min.js` and `version.json`
together. No Firebase, Cloudflare or hosting deployment is required for this change.
