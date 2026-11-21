# Foggy Dude Energy: handoff for Claude Code

## What this is

A one-page site for Samuel's home-mixed energy and vitamin drink, "Foggy Dude Energy" (first built as "UTREHD Energy"; Samuel is the Foggy Dude). It lives in the public repo `Utrehd/FoogyDude-Energy` and is served by GitHub Pages from `main` at https://utrehd.github.io/FoogyDude-Energy/.

Live preview (claude.ai artifact, private to Samuel): https://claude.ai/artifact/SK6SvLo5wy5Qy2PLScuQyL

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole site in one file (HTML, CSS, JS inline). Google Fonts: Big Shoulders Display (wordmark, headings) + Fragment Mono (everything else). |
| `foggy-dude.webp` | The Foggy Dude silhouette used in the fog scene |
| `foggy-dude-energy.svg` | Animated hero image (wordmark, fog, Foggy Dude, heartbeat line) |
| `foggy-dude-energy.jpg` | Still 1200×630 image for link previews (`og:image`), rendered from the SVG with Playwright |
| `.nojekyll` | Makes GitHub Pages serve files as they are |
| `README.md` | Public-facing repo readme |
| `CLAUDE.md` | This file |

## Repo and Pages (done)

The repo is public and Pages deploys from `main` / root. Samuel asked for a public page, which overrides the earlier "private repo" plan.

## Look

- Dark only (no light theme): deep navy background, cool blue fog, neon accents. Tokens at the top of `index.html`: `--bg`, `--ink`, `--muted`, `--track`, `--pulse` (pink accent), `--neon` (cyan, used on the kicker), `--warn`. Hardcore mode swaps `--pulse` to orange and `--neon` to amber on `body[data-mode="hardcore"]`.
- "ENERGY" in the wordmark is a neon outline in `--pulse`. On phones (560px and under) the wordmark is two lines: FOGGY DUDE on one line, sized at 19vw so it fits, then ENERGY.
- The Everyday / Hardcore switch is two inline-SVG drink cans side by side (also on mobile). The chosen can turns to face you, glows and floats; the other is tilted and dimmed. Everyday: silver can, pink bolt. Hardcore: black can, orange label, double bolt.
- Ingredient rows show only name, amount and (where there is one) the bar. Need/max text ("Reference"), short-term and long-term effects all sit in the dropdown. A flagged ingredient gets a small "!" before its name.

## Fog scene

- `.scene` is a fixed layer behind `main`: sky gradient with a backlight, three fog layers (`.f1`–`.f3`), and the Foggy Dude (`foggy-dude.webp`, 811×1100 with transparency) between the far and near fog.
- Fog tiles are inline SVG `feTurbulence` data URIs (1200×700, `stitchTiles`), so there are no image files to load.
- The script at the bottom drifts the fog sideways, moves layers at different speeds on scroll (`data-depth`), nudges them with the pointer, and sets `--clear` (0 to 1 over the first screen) so the fog thins as you scroll. Hardcore mode drifts the fog faster.
- `prefers-reduced-motion` stops the drift and parallax; the fog still thins on scroll.
- `foggy-dude.webp` comes from a photo of Samuel: cut out with rembg (`u2net_human_seg`), then darkened to a navy silhouette with a faint trace of the photo, a cool backlight rim, film grain, faint scratches, and the legs fading into fog. The original photo is not in the repo.

## Task 2 (optional, ask first): restyle with his "Pulse" style

Samuel has a design style called "Pulse" in one of his GitHub repos. The chat couldn't access GitHub, so the current look is a stand-in.

- Find it: `gh repo list --limit 100` and search for "pulse" in repo names, CSS, or design-token files.
- Map its tokens onto the CSS variables at the top of `index.html`: `--bg`, `--ink`, `--muted`, `--track`, `--pulse` (accent), `--neon`, `--warn`. The site is dark only; keep the hardcore-mode override on `body[data-mode="hardcore"]`.
- Swap fonts if Pulse defines them (Google Fonts `<link>` plus the font stacks).
- Update `foggy-dude-energy.svg` colors to match, then re-render `foggy-dude-energy.jpg` (e.g. Playwright screenshot at 1200×630).
- Don't change the content, numbers, or layout rules.

## Design rules Samuel asked for

- Modern and minimal, **no boxes or cards**, little text.
- Two versions with a toggle: **Everyday** and **Hardcore session**.
- A visual list of every ingredient. Anything with a recommended dose (`REF`: daily need or studied dose) or a max gets a bar, and every bar uses the same marks: a **green** tick at 30% (recommended) and a **red** tick at 70% (max). If only one is known, only that mark shows. A recommended dose that is not below the max (creatine, magnesium) is left out, so those show red only. The white fill is piecewise: with both marks, 0 to recommended fills 0–30% and recommended to max 30–70%; red only, 0 to max fills 0–70%; green only, 0 to recommended fills 0–30% and it reaches 100% at 3x the recommended dose (that is not a warning, there is no max). Beyond a max it fills 70–100% (full at twice the max) and turns orange. See `barPos()` and `recOf()` in the script.
- Ingredients with neither a recommended dose nor a max go in a second section, "No bar yet", with name and amount only. Add a `REF` entry or a `max` and the row moves up to the bars section on its own.
- Tap a row for the reference text and short-term and long-term effects.
- Ingredients are one plain list per section, in the order they are defined in `GROUPS`. There are no category headings.

## How the data works (all in the `<script>` of `index.html`)

- `MODES`: how much of each product goes in. Everyday: XBEAM 1 scoop, Sponser ⅓ serving (5 g), tetesept 1 tablet, creatine 3 g. Hardcore: same but Sponser full (15 g), and the drink swaps: Everyday uses one 330 ml can of El Tony Mate Classic (`et`, Migros), Hardcore one 500 ml can of Monster Energy Original, the sugared green one (`mo`). Water tops the bottle up to 600 ml in both (`water`: 270 ml Everyday, 100 ml Hardcore). Caffeine is per mode in `caff`: Everyday 226 mg (XBEAM 150 + El Tony 76), Hardcore 310 mg (XBEAM 150 + Monster 160). Rows whose total is 0 in a mode (e.g. L-carnitine L-tartrate in Everyday) are hidden, and a row that goes over its max, or caffeine above 200 mg at once, gets the "!" marker, an orange bar and a warning line in its dropdown.
- `RECIPE`: the "Cook list" list, inside a dropdown that is closed by default. The summary line and the how-to line stay visible below it.
- `GROUPS`: every ingredient with `src` = amount per full serving of each product (`xb` XBEAM, `sp` Sponser, `tet` tetesept, `cr` creatine), optional `max`, `flag`, and short/long-term effect text.
- `REF`: daily need (EU NRV) or studied dose per ingredient, drawn as the tick.
- `MAXTXT`: custom wording for non-standard limits.
- Item options: `once` (single-dose limit, gives the "sip it slowly" warning), `note` (reference text for an item with no max and no recommended dose), `more` (a product adds an amount the label does not state).

## Sources behind the numbers

- **Monster Energy Original (500 ml, with sugar):** per 100 ml: 11 g sugars (55 g a can), 201 kJ, 0.19 g salt, caffeine 32 mg (160 mg a can), taurine 0.4% (2 g), panax ginseng root extract 0.08% (400 mg), L-carnitine L-tartrate 0.04% (200 mg), guarana seed extract 0.002% (10 mg). B vitamins per 100 ml: niacin 8.5 mg, B6 0.8 mg, B2 0.7 mg, B12 2.5 µg. The label also lists inositol, D-glucuronolactone, maltodextrin, sucralose, salt, preservatives and colour with no amounts, so only inositol shows as "1 g+". Read from the Open Food Facts German entry (barcode 5060335635228, incomplete) and the UK label text (barcode 5060896623122); the vitamin amounts come from the UK label and a German retailer listing, not a Swiss can. Check them against a Swiss can if exact numbers matter.
- **El Tony Mate Classic (330 ml can):** per 100 ml: 23 mg caffeine (about 76 mg a can), 5.8 g sugar (about 19 g a can), 25 kcal. Ingredients: mate infusion, cane sugar, lemon juice, carbon dioxide, natural caffeine, natural flavouring. From the El Tony facts page (eltonymate.com/facts/el-tony-mate/) and a Blick article (76 mg a can); Migros itself lists no nutrition on its page. Migros also has its own "Lamate", which has no label data here.
- **Product labels:** GymBeam XBEAM Energy Powder (9 g scoop, 150 mg caffeine), Sponser Mental Focus (15 g daily ration, art. 45003), tetesept A-Z Complex effervescent, Swiss version (tetesept.ch), QNT Metapure Creatine Monohydrate (3 g serving).
- **Daily max:** EFSA "Overview on Tolerable Upper Intake Levels" (version 11, Aug 2025). Examples: B6 12 mg, folic acid 1000 µg (supplements), magnesium 250 mg (supplements), vitamin A 3000 µg (retinol only), zinc 25 mg, selenium 255 µg; iron 40 mg and manganese 8 mg are EFSA "safe levels".
- **Other limits:** caffeine 400 mg/day and 200 mg at once (EFSA). Creatine 3 g/day (EFSA-assessed). Observed safe levels: taurine 3 g, L-carnitine 2 g (Shao & Hathcock). Citrulline no-effect level 24 g. Astaxanthin 8 mg (EU novel food max). Choline 3.5 g (US UL).
- **Daily need:** EU nutrient reference values (Reg. 1169/2011); choline 400 mg is the EFSA adequate intake.
- **Studied doses:** caffeine 75 mg (EFSA alertness claim), sage/Cognivia 600 mg (Wightman et al. 2021), bacopa 300 mg, ashwagandha 600 mg, lutein 10 mg and zeaxanthin 2 mg (AREDS2), citrulline 6 g.
- **Ashwagandha:** flagged because EU agencies (BfR 2024) haven't set a safe long-term level. Samuel knows and accepts this; keep the flag but don't add more warnings.

## Source ladder for recommended doses and maxes

Every ingredient should have a green mark (recommended dose), a red mark (max), or both. Use sources in this order and stop at the first that gives a usable number:

1. **EU:** EFSA (UL opinions, health-claim doses, NRVs), EMA/HMPC herbal monographs, EU novel-food authorisations.
2. **Other authorities:** Health Canada (NNHPD monographs, the most consistent single source, covering all the amino acids here) and WHO/FAO.
3. **Human trials:** the dose used in the main trials, marked "Studies use".

Numbers added this way (`REF` for green, `max` + `MAXTXT` for red; each reference line names its source):

| Ingredient | Green | Red | Source |
|---|---|---|---|
| L-Tyrosine | none | 3.6 g | Health Canada Workout Supplements monograph (its single L-Tyrosine monograph also allows 10–20 g for occasional use, and the two disagree) |
| Glycine | none | 1.8 g | Health Canada Workout Supplements monograph |
| Inositol | none | 650 mg | Health Canada multivitamin monograph (complementary ingredient cap). Trials saw side effects only at 12 g, so this cap is conservative, and both versions show it as over the max |
| L-Threonine | about 1.05 g | none | WHO/FAO 15 mg per kg a day at an assumed 70 kg. Health Canada caps free threonine at 301 mg, which is below the dietary need, so it is not used as the red mark and only appears in the text |
| L-Theanine | 200 mg | none | Health Canada 200–250 mg a day |
| Phosphatidylserine | none (same as max) | 300 mg | Health Canada, soy PS recommended maximum daily intake |
| Beta-carotene | none | 18 mg | Health Canada. EFSA 2024 sets no UL, smokers should avoid supplements with it |
| Rhodiola | 144 mg | 400 mg (200 mg at once) | EMA/HMPC monograph, dry extract DER 1.5–5:1. Hardcore is above the single-dose limit and gets the "!" |
| Ginseng | 196 mg | 525 mg | EMA/HMPC monograph, dry extract DER 3–7:1, ethanol 50–60%. Health Canada has 200–600 mg |
| Lemon balm | 600 mg | none | Kennedy 2002 trial, standardised extract. EMA and Health Canada give doses per gram of dried herb, which cannot be compared with mg of extract without the extract ratio |
| Mango leaf (Zynamite) | 300 mg | none | Single-dose trial (sponsor-funded). No official figure exists |
| Chokeberry (BrainBerry) | 90 mg | none | 24-week trial (sponsor-linked). No official figure exists |
| Guarana | no bar | no bar | EMA (450 mg seed powder) and Health Canada (1–3 g seed) are for whole seed and the extract strength is unknown. Its caffeine is in the caffeine bar. A `note` explains this in the dropdown |

Not read by the research: the EU Novel Food Catalogue entries, TGA, UK FSA/EVM, BfR and JECFA for most of these. Add a `max` / `REF` entry for any ingredient to move it into the bars.

## Keep in mind

- Don't add health claims beyond what's there; the effect texts are deliberately hedged.
- Footer says: not for under-18s, pregnancy or breastfeeding; one bottle a day; not medical advice. Keep it.
- The name is "Foggy Dude Energy". If it changes, update `index.html` (title, meta, `aria-label`, wordmark spans, footer sign-off) and both images.
