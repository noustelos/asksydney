# asksydney.ai

Design-only landing page for **asksydney.ai** — third property in the Noustelos Studio "Ask" network
([asksantorini.ai](https://asksantorini.ai) → [asksingapore.ai](https://asksingapore.ai) → asksydney.ai).

Everything is a single file, [index.html](index.html): vanilla HTML/CSS/JS, no frameworks, no build
step, no backend, no asset files (favicon and decorative art are inline SVG).

## What it does

- **Sells the domain.** The acquisition pill above the topbar is the primary conversion point
  (plain `mailto:`, no forms).
- **Previews the concierge concept.** The chat window is an honest **scripted demo** — four
  hardcoded Q→A pairs behind the category chips (Restaurants / Hotels / Car rental / Directions),
  with a typing-dots + word-by-word reveal. Zero AI, zero network; a disclosure caption links to
  the live concierge at asksantorini.ai.
- **Live Sydney clock** in the topbar via `Intl.DateTimeFormat` (`Australia/Sydney`), with the
  AEST/AEDT label derived from the formatter (Sydney observes DST). The weather line is a static
  mockup awaiting live data.

## Design — "Harbour Light"

Sun-warmed coastal editorial: sandstone paper (`#FBF8F2`), deep harbour navy ink (`#12222E`),
Pacific harbour blue (`#1A6B96`) as the accent family, and sunset coral (`#D9451F`) strictly
reserved for the two conversion points (Send button + acquisition pill). Signature decoration:
thin line-art of the Opera House sails with the Harbour Bridge arch faint behind. Type:
**Fraunces** (display serif) + **Albert Sans** (UI). All tokens live in `:root` — see
[CLAUDE.md](CLAUDE.md) for the full architecture.

## Run

No build. Open `index.html` in a browser, or:

```bash
python3 -m http.server 8000   # http://localhost:8000
```

## Deploy

Cloudflare Pages, git-connected: **push to `main` = instant live** on asksydney.ai.
Work on branches, merge deliberately, and verify the deployment landed in the Pages dashboard
after every push.
