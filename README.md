# Simone & Haris – wedding website

Astro, static output, deployed to GitHub Pages (`simonebealive.github.io/matrimonio-sh`).

```
npm install
npm run dev      # local
npm run build    # static build to dist/
```

One manual step: repo Settings → Pages → Source: "GitHub Actions".

Date-driven constants: `WEDDING_AT` in `src/components/Countdown.astro`,
`RSVP_DEADLINE` in `src/components/RegistrationLink.astro`.

## Open points

- External form URL (`FORM_URL` in `RegistrationLink.astro`, currently a placeholder).
- Full-resolution original photos (current ones are extracted from the Canva PDF, same filenames in `src/assets/photos/`).
- Text for "Anreise & Unterkunft" (TODO lines in `src/pages/overview.astro`).

## Deviations from the Canva mockup

- Valerie's phone number removed.
- "Anreise & Unterkunft" block added (placeholder).
- Live counter added on the Countdown page.
- Mobile layouts are new.
- Canva fonts replaced by Pinyon Script, Playfair Display, Archivo.
