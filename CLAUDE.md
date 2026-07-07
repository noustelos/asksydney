# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A **design-only** landing page for asksydney.ai — third property in the Noustelos Studio "Ask" network (asksantorini.ai → asksingapore.ai → asksydney.ai). The page lives in a single file: [index.html](index.html) — vanilla HTML/CSS/JS, no frameworks, no build step, no backend, no dependencies — plus three static discovery files at the root ([robots.txt](robots.txt), [sitemap.xml](sitemap.xml), [llms.txt](llms.txt)). Standalone: zero sister-site dependency (sister links only).

There is intentionally **no AI or network logic**. The chat is a **SCRIPTED DEMO**: four hardcoded Q→A pairs matching the chip prefills play a typing-dots + word-by-word reveal; any other input gets a fallback line. All client-side — the real backend later replaces the `SCRIPTED` map + `respond()` in the inline script (the hooks stay put). Treat this repo as the visual source of truth; do not add fetch/API/state-management code unless explicitly asked.

**Honesty rule: the demo must not fake live-AI signals.** The bold `.caption` under the category chips discloses the scripted demo and links to the live concierge at [asksantorini.ai](https://asksantorini.ai) (the only disclosure line — there is no separate `.disclaimer`), and the chat-window status label reads "Live Demo · Ask Sydney AI". Keep both honest until the real backend lands.

The commercial goal of the page is to **sell the domain**: the `.acquire` pill above the topbar states availability and links out via `mailto:` to `info@asksantorini.ai` (the only outbound contact — keep it a plain mailto, no forms; switch to `info@asksydney.ai` once that mailbox is set up). The `.foot` footer links to the five sister domains [asksantorini.ai](https://asksantorini.ai), [asksingapore.ai](https://asksingapore.ai), [askaustralia.ai](https://askaustralia.ai), [asknewyork.ai](https://asknewyork.ai) and [askmykonos.ai](https://askmykonos.ai), and the bottom-left `.studio-mark` links to [noustelos.gr](https://noustelos.gr) — all signal the brand network. `<head>` carries the canonical URL, Open Graph/Twitter cards (pointing at the root [og-image.png](og-image.png), 1200×630 — the repo's only binary asset), an inline SVG favicon, and JSON-LD, so shared links preview well for prospective buyers. The og image is a headless-Chrome screenshot of [og-card.html](og-card.html), a standalone card mirroring the hero (brand lockup, "Just Ask", tagline, coral acquisition pill, harbour line-art) — edit that file and re-screenshot at 1200×630 to regenerate; keep its copy/colors in sync with the page. Root-level [robots.txt](robots.txt) (allow-all + sitemap pointer), [sitemap.xml](sitemap.xml) (single URL) and [llms.txt](llms.txt) (plain-language summary for AI crawlers — sale status, contact, sister network) handle search/LLM discovery; keep the sister-domain list in llms.txt in sync with the footer.

## Deploy

⚠️ **Cloudflare Pages, git-connected: push to `main` = INSTANT LIVE on asksydney.ai** (no branch previews, like the sister sites). Work on a branch, merge deliberately, verify live after push — the askSingapore webhook once died silently and needed a repo reconnect in the Pages dashboard, so check the deployment actually landed. Rollback = revert commit + push (or dashboard rollback).

## Run

No build. Open [index.html](index.html) directly in a browser, or serve it:

```bash
python3 -m http.server 8000   # then visit http://localhost:8000
```

## Architecture

The page is one `.hero` section with three layers:

1. **Ambient decoration** (`.deco-*`, `aria-hidden`) — the signature `.deco-harbour` "Harbour View" (thin harbour-blue line-art, inline SVG, bottom-left: Opera House sails with the Bridge arch faint behind and swell lines beneath) plus the `.deco-weave` adaptive theme layer (transparent by default, dissolves in a pattern per query theme). Purely cosmetic.
2. **`.inner`** — the `.acquire` domain-for-sale pill, the topbar (brand mark + wordmark + Sydney clock) and `.main`, which holds the `.hero-row` (the "Just Ask" title on the left, the `.chat` mock window on the right), the `.closing` block, and the `.foot` sister-domains footer.
3. **Floating anchors** — the `.cta-pill` "Ask Sydney AI" button (bottom right) and the `.studio-mark` Noustelos Studio attribution (bottom left; joins the flow under the footer on narrow screens).

### Styling system

Design language: **"Harbour Light"** (sun-warmed coastal editorial). All design decisions are centralized as CSS custom properties in `:root`. Change colors there, not at call sites:

- `--paper: #FBF8F2` — warm sandstone off-white page field; `--ink: #12222E` — deep harbour navy-black body text.
- `--harbour: #1A6B96` (+ `--harbour-deep`) — primary Pacific harbour-blue accents; `--mist: #E6ECEF` — soft blue-grey surfaces/dividers.
- `--sunset: #D9451F` — sunset coral, **CTAs ONLY**: the Send button (`--send-grad`) and the acquisition pill. Nowhere else. This scarcity is the page's conversion logic.
- `--diamond-grad`, `--wordmark-grad` — harbour/ink gradients for the brand mark and wordmark.
- `--font-display` (**Fraunces**, sun-warmed editorial serif — hero title roman, with "Ask" in harbour blue via `.display .accent`) and `--font-ui` (Albert Sans) — loaded from Google Fonts in `<head>`.

Animations are defined as `@keyframes sy*` near the top of the `<style>` block (`syMarkShimmer`/`syDiamondPulse` "living" logo, `sySendGlow`/`sySendSpark` Send-button feedback, `syLivePulse`, `syRise` staged page entrance). No continuous full-surface animations or `backdrop-filter` — keep it light for older machines. All motion is gated behind a `prefers-reduced-motion: reduce` guard — extend that rule when adding new animations. Keyboard focus uses a global harbour `:focus-visible` outline (the input row carries the ring via `:focus-within` instead of the field itself). Layout is non-wrapping by design and only stacks below the `max-width: 760px` media query.

### Interactive behaviour (client-side only, no network)

The inline `<script>` drives the scripted demo and presentation feedback — still zero AI/network. Keep additions on this side of that line:

- **Scripted chat** — `#chat-log` is a nested-scroll message list (max-height, thin harbour scrollbar; the greeting is the first bot message). `submitQuery()` appends the user bubble (right-aligned, deeper mist); `respond()` shows typing dots (~800ms) then streams the answer word-by-word at 45ms/word (opacity-only spans). Under `prefers-reduced-motion` the **streaming still plays** (30ms/word, instant pop) — only the dots pulse and smooth scrolling are dropped. Copy lives in the `SCRIPTED` array + `FALLBACK` string.
- **Send button** toggles `.is-ready` (sunset glow) when `#ask-input` holds text, and replays a `.spark` sweep; Send/Enter with text runs `submitQuery()`.
- **Adaptive background** — `detectTheme()` matches query keywords and swaps a `theme-coast` / `theme-luxury` class on `.hero`, which dissolves a faint pattern into `.deco-weave` (rolling swell lines / woven pinstripe) via a brief `weave-shift` dissolve. Keyword→theme maps live in the `THEMES` array.
- **Live Sydney clock** — `#syd-date` + `#syd-clock` in the topbar show the current Sydney date and time via `Intl.DateTimeFormat({ timeZone: 'Australia/Sydney' })` (computed locally, no network), refreshed on a 15s interval. Editorial lockup, top to bottom: a harbour `.clock-date` line, the large light `.clock-time` numeral with a dimmed `.cc` colon, then a small tracked `.clock-meta` line (live dot · `#syd-tz` zone label · "24/7 Harbourside Availability"). ⚠️ Sydney observes DST (AEST/AEDT) — the zone label is **derived** each tick from the formatter's `timeZoneName: 'short'` part, never hardcoded. `tickClock()` writes the date text, the `HH<span class="cc">:</span>MM` markup, and the zone label each tick.

### Backend wiring hooks (placeholders only)

These IDs/hooks are where the real backend connects later (replacing the scripted `respond()`) — keep them in place:

- `#ask-input` — chat input field
- `#ask-send` — Send button (runs the scripted `submitQuery()`)
- `#cta-ask` — floating CTA pill
- `.chip[data-prefill]` — category chips: **prefill** `#ask-input`, then auto-send after 300ms (unless a reply is playing)
- `#syd-temp` — local-weather line under the clock; a static mockup (`18°C · Mostly Sunny`) awaiting live data (real weather needs an API, which is out of the design-only scope)
- `#chat-log` — conversation log the backend will append to
