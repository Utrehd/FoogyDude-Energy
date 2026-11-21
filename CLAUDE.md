# UTREHD Energy: handoff for Claude Code

## What this is

A one-page site for Samuel's home-mixed energy and vitamin drink, "UTREHD Energy". It was built in a claude.ai chat and needs to go into a new **private** GitHub repo on Samuel's **personal** account (not an organization).

Live preview (claude.ai artifact, private to Samuel): https://claude.ai/artifact/SK6SvLo5wy5Qy2PLScuQyL

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole site in one file (HTML, CSS, JS inline). Google Fonts: Big Shoulders Display + Hanken Grotesk, with Anton/Impact fallbacks. |
| `utrehd-energy.svg` | Animated hero image (wordmark + heartbeat line) |
| `utrehd-energy.png` | Static 1200×630 image for link previews (`og:image`) |
| `README.md` | Public-facing repo readme |
| `CLAUDE.md` | This file |

## Task 1: create the private repo and push

1. Check GitHub CLI auth: `gh auth status`. If not logged in, ask Samuel to run `gh auth login`.
2. Confirm which account is active and that it's his personal account, not a work org.
3. From the folder with these files:
   ```bash
   git init -b main
   git add .
   git commit -m "Initial UTREHD Energy site"
   gh repo create utrehd-energy --private --source=. --remote=origin --push
   ```
4. Show Samuel the repo URL.

**GitHub Pages:** Pages on a private repo needs a paid GitHub plan (Pro or above). Ask Samuel before enabling Pages or making the repo public. If he wants it live: Settings → Pages → Deploy from a branch → `main` / root.

## Task 2 (optional, ask first): restyle with his "Pulse" style

Samuel has a design style called "Pulse" in one of his GitHub repos. The chat couldn't access GitHub, so the current look is a stand-in.

- Find it: `gh repo list --limit 100` and search for "pulse" in repo names, CSS, or design-token files.
- Map its tokens onto the CSS variables at the top of `index.html`: `--bg`, `--ink`, `--muted`, `--track`, `--pulse` (accent), `--warn`. Keep separate light and dark values (the `prefers-color-scheme` block and `[data-theme]` overrides), and keep the hardcore-mode accent override on `body[data-mode="hardcore"]`.
- Swap fonts if Pulse defines them (Google Fonts `<link>` plus the font stacks).
- Update `utrehd-energy.svg` colors to match, then re-render `utrehd-energy.png` (e.g. Playwright screenshot at 1200×630).
- Don't change the content, numbers, or layout rules.

## Design rules Samuel asked for

- Modern and minimal, **no boxes or cards**, little text.
- Two versions with a toggle: **Everyday** and **Hardcore session**.
- A visual list of every ingredient: red line = one bottle, short black tick = daily need or studied dose, line ends at the daily max, fading line = no max set. Tap a row for short-term and long-term effects.

## How the data works (all in the `<script>` of `index.html`)

- `MODES`: how much of each product goes in. Everyday: XBEAM 1 scoop, Sponser ⅓ serving (5 g), tetesept 1 tablet, creatine 3 g. Hardcore: same but Sponser full (15 g). Caffeine is the same in both (170–210 mg) because XBEAM is capped at 1 scoop a day.
- `RECIPE`: the "In the bottle" list.
- `GROUPS`: every ingredient with `src` = amount per full serving of each product (`xb` XBEAM, `sp` Sponser, `tet` tetesept, `cr` creatine), optional `max`, `flag`, and short/long-term effect text.
- `REF`: daily need (EU NRV) or studied dose per ingredient, drawn as the tick.
- `MAXTXT`: custom wording for non-standard limits.

## Sources behind the numbers

- **Product labels:** GymBeam XBEAM Energy Powder (9 g scoop, 150 mg caffeine), Sponser Mental Focus (15 g daily ration, art. 45003), tetesept A-Z Complex effervescent, Swiss version (tetesept.ch), QNT Metapure Creatine Monohydrate (3 g serving). Mate tea bag estimated at 20–60 mg caffeine.
- **Daily max:** EFSA "Overview on Tolerable Upper Intake Levels" (version 11, Aug 2025). Examples: B6 12 mg, folic acid 1000 µg (supplements), magnesium 250 mg (supplements), vitamin A 3000 µg (retinol only), zinc 25 mg, selenium 255 µg; iron 40 mg and manganese 8 mg are EFSA "safe levels".
- **Other limits:** caffeine 400 mg/day and 200 mg at once (EFSA). Creatine 3 g/day (EFSA-assessed). Observed safe levels: taurine 3 g, L-carnitine 2 g (Shao & Hathcock). Citrulline no-effect level 24 g. Astaxanthin 8 mg (EU novel food max). Choline 3.5 g (US UL).
- **Daily need:** EU nutrient reference values (Reg. 1169/2011); choline 400 mg is the EFSA adequate intake.
- **Studied doses:** caffeine 75 mg (EFSA alertness claim), sage/Cognivia 600 mg (Wightman et al. 2021), bacopa 300 mg, ashwagandha 600 mg, lutein 10 mg and zeaxanthin 2 mg (AREDS2), citrulline 6 g.
- **Ashwagandha:** flagged because EU agencies (BfR 2024) haven't set a safe long-term level. Samuel knows and accepts this; keep the flag but don't add more warnings.

## Keep in mind

- Don't add health claims beyond what's there; the effect texts are deliberately hedged.
- Footer says: not for under-18s, pregnancy or breastfeeding; one bottle a day; not medical advice. Keep it.
- The name is spelled "UTREHD" as Samuel wrote it. If he says otherwise, change it in `index.html` (title, `aria-label`, wordmark spans) and both images.
