# Plan: Wedding website for Simone & Haris

## Context

Haris wants a website for the wedding of Simone & Haris on 07.05.2027 at I Tre Poggi,
Canelli (Italy). Simone made a Canva mockup (`input/Webseite-PDF.pdf` plus four PNG
exports). The mockup is the spec. The repo is empty apart from a devcontainer with Node
and is hosted at `github.com/Simonebealive/matrimonio-sh`. Guests open the link from the
invitation, mostly on phones. The site has a fixed end date and must stay presentable
without manual edits after the RSVP deadline and after the wedding.

## Decisions (all settled in the grilling session)

| Topic | Decision |
|---|---|
| Fidelity | Reproduce the mockup. Deviate only where the web forces it. Flag every deviation. |
| Pages | Home, How they met, Overview, Countdown. English nav labels as in the mockup. |
| Languages | Mixed on purpose (German body, EN/IT headlines, BS/HR programme). No switch. |
| Anmeldeformular | Link to an external form, new tab. URL is an open point, placeholder until then. |
| After 31.10.2026 | Button text changes automatically to a "deadline passed, contact Simone" line. |
| Countdown | Live counter to 2027-05-07 15:00 Europe/Rome. "Oggi!" on the day, "Grazie" line after. |
| Added block | "Anreise & Unterkunft" on the Overview page, placeholder structure, lines marked TODO. |
| Removed | Valerie's phone number. Contact line keeps only "Simone". |
| Visibility | Not indexed (`noindex` meta plus `robots.txt`). Open to anyone with the link. |
| Stack | Astro, static output. |
| Hosting | GitHub Pages via GitHub Actions. Address `simonebealive.github.io/matrimonio-sh`. |
| Photos | Extracted from the PDF now, committed to the repo. Originals swapped in later, same placement. |
| Fonts | Free fonts, self-hosted via Fontsource. |
| Copy | Inline in the four page files. No content layer. |
| Mobile | Mobile first. Four short nav links in a row, no hamburger. |
| Domain model | `GLOSSARY.md` with five terms. No ADRs. |

## Design tokens (derived from the mockup, calibrate with a colour picker during build)

Colour
- `--yellow` #FBEA9B: nav bar, display type, body text on dark blocks
- `--slate` #5F7378: story and "Gut zu wissen" blocks
- `--orange` #D98A3F: second story block
- `--ink` #2B3A3A: text on the yellow nav
- `--overlay` rgba(20, 30, 20, 0.45): wash over full-bleed photos

Type (two families, clearly distinct, plus body)
- Script display: Pinyon Script (names, "It began with a zucchetti", dresscode quote, "Non possiamo vedere l'ora")
- Serif display: Playfair Display, tight tracking (dates, "wedding day", "Gut zu wissen", "Program", "Dresscode")
- Body: Archivo (all paragraphs, nav, programme table)

Layout
- Full-bleed photo heroes with centred display type (Home, Overview top, Countdown).
- Story page: two stacked colour blocks, photos bleed to the edge, text column max 60ch.
- Mobile: every two-column block stacks, photos keep edge bleed, text stays left aligned.
- Programme: two-column time/label list, times right aligned in a dark rounded card as in the mockup.

Principle
- The script type is the one bold element. Everything else stays quiet.
- No motion except the ticking counter.

## File layout

```
astro.config.mjs            site + base '/matrimonio-sh', output 'static'
package.json                astro, @fontsource/pinyon-script, @fontsource/playfair-display, @fontsource/archivo
public/robots.txt           User-agent: * / Disallow: /
public/favicon.svg
src/styles/global.css       tokens, type scale, nav, shared blocks
src/layouts/Base.astro      <head> (noindex, fonts, title), Nav, <slot>
src/components/Nav.astro    yellow bar, four links, current page bold
src/components/Countdown.astro       markup + client script (date math, three states)
src/components/RegistrationLink.astro button; swaps text after the deadline (client script)
src/pages/index.astro       Home
src/pages/how-they-met.astro
src/pages/overview.astro
src/pages/countdown.astro
src/assets/photos/*.jpg     extracted from the PDF
.github/workflows/deploy.yml  withastro/action + actions/deploy-pages
GLOSSARY.md
```

Both date-driven components read a single constant each (`WEDDING_AT`, `RSVP_DEADLINE`)
with explicit offsets (`2027-05-07T15:00:00+02:00`, `2026-10-31T23:59:59+01:00`) so the
result is the same in every visitor timezone.

## Steps

1. Scaffold Astro (minimal template), set `site` and `base`, add Fontsource packages.
2. Extract photos from `input/Webseite-PDF.pdf` with `pdfimages` (poppler) or PyMuPDF
   (run Python with `-I`, output into `src/assets/photos/`). Name them by page and role
   (`home-bouquet.jpg`, `story-lake.jpg`, `story-portrait.jpg`, `story-fence.jpg`,
   `overview-chairs.jpg`, `overview-map.jpg`, `countdown-villa.jpg`, `lemons.png`, `flowers.png`).
3. Write `global.css` tokens and `Base.astro` with `noindex` meta and Nav.
4. Build the four pages with the mockup copy verbatim, Astro `<Image>` for photos.
   Overview adds the "Anreise & Unterkunft" block (TODO placeholder lines) under
   "Gut zu wissen". Countdown page drops Valerie's number.
5. Add `Countdown.astro` and `RegistrationLink.astro` with their three/two states.
6. Add `robots.txt`, favicon, deploy workflow.
7. Write `GLOSSARY.md`:
   - Page: one of the four nav destinations.
   - Section: a full-width block inside a page (hero, story block, Gut zu wissen, Program).
   - Programme item: one time plus label row of the wedding-day programme.
   - Registration: the guest's sign-up via the external form (Anmeldeformular).
   - Countdown target: the moment the counter counts to, 07.05.2027 15:00 Europe/Rome.
8. Record open points in the README: form URL, original photos, Anreise text.

## Deviations from the mockup to flag to Simone

- Valerie's phone number removed.
- "Anreise & Unterkunft" block added (placeholder).
- Live counter added on the Countdown page.
- Mobile layouts are new, not in the mockup.
- Canva fonts replaced by Pinyon Script, Playfair Display, Archivo.

## Verification

1. `npm run build` succeeds with zero warnings; `dist/` contains the four pages under `/matrimonio-sh/`.
2. `npm run preview`, then screenshots at 390 px and 1366 px of each page (Playwright).
   Place next to the mockup PNGs and compare block order, colours, type roles.
3. Countdown states: override `Date` in the browser console to a day before, the wedding
   day, and a day after; confirm counter, "Oggi!", and "Grazie" line.
4. Registration link: same override for 2026-10-30 and 2026-11-01; confirm the text swap.
5. `curl` the built `index.html` for `<meta name="robots" content="noindex">` and check `robots.txt`.
6. Lighthouse on mobile: performance above 90, no accessibility errors; focus rings visible on nav.
7. Push to `main`, confirm the Actions workflow deploys and the Pages URL serves the site.
   One manual step for Haris: in repo Settings, Pages, set source to "GitHub Actions".

## Open points (do not block the build)

- External form URL.
- Full-resolution original photos.
- Text for "Anreise & Unterkunft".
