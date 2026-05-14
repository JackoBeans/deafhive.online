# DeafHive — project brief for Claude Code

This file is read automatically at the start of every Claude Code session in this folder. Keep it up to date. When decisions change, edit this file.

Audience: you (Claude Code), and Mark's future self in six months.

---

## What DeafHive is

A single-page static landing site for **DeafHive**, an information and inclusion hub for the Deaf community in the UK. It points visitors to a community directory, a BSL events archive, role-model stories, and an invitation to contribute.

The site is mostly an entry point — most of the real content lives in two Softr-hosted views that are embedded as iframes:

- Community Directory — Deaf groups, events, services, support
- BSL Events & Community Video Archive

So the HTML file's job is: welcome people in BSL, explain what DeafHive is, then surface the directories and a few BSL videos.

## Voice

**No formal voice guide.** Keep the existing tone — warm, plain, factual, first-person plural ("we created DeafHive…"). The current copy uses capital **"Deaf"** for the community; match that. **Do not** apply the Conversant voice rules (lowercase deaf, no superlatives, British English locks). DeafHive has its own register and audience.

If asked to rewrite copy, prefer:
- Plain, short sentences over long marketing prose
- Active voice
- Honest about what's pending (we say "you'll find" only when it's actually there)

## Tech stack

- **Single file:** `index.html`. Vanilla HTML, CSS, and JS — no framework, no build step.
- **Fonts:** `Raleway` (400/600/700/800/900) from Google Fonts.
- **Background:** SVG hex grid generated client-side at the bottom of the file. Re-renders on resize (debounced).
- **YouTube embeds:** `youtube-nocookie.com` iframes with `loading="lazy"`.
- **Softr embeds:** two `<iframe>`s — directory + events archive. Treat the Softr URLs as opaque content sources; we do not edit them here.
- **No JS dependencies.** No npm, no node_modules.

Do not introduce a framework, a bundler, Tailwind, React, etc. without asking. The site is intentionally simple.

## File / repo layout

```
index.html          ← the entire site (HTML, embedded CSS, embedded JS)
og.svg              ← source for the social-share / Open Graph image
og.png              ← rendered 1200x630 OG image, referenced from <head>
CNAME               ← GitHub Pages custom-domain marker (deafhive.online)
README.md           ← public, on GitHub
CLAUDE.md           ← this file
.gitignore
.claude/launch.json ← local dev preview config (gitignored)
```

If a new asset is needed (image, font, etc.), put it next to `index.html` and reference it relatively.

### Regenerating `og.png`

The OG image is rendered from `og.svg` via librsvg (`brew install librsvg` once):

```sh
rsvg-convert -w 1200 -h 630 og.svg -o og.png
```

Edit the SVG (brand colours, copy, layout), then re-run. Commit both files together.

## Sections in `index.html` (in order)

1. Sticky `<nav>` with logo + 4 links + mobile hamburger
2. `#about` — Hero with **Welcome to DeafHive** video (`_raNeUTdE6Q`)
3. **What is DeafHive?** — three bullets + video (`TAG5JM7zhTU`)
4. **Why We Created DeafHive?** — three bullets + video (`c29KSuD2iQc`)
5. **What You Will Find on DeafHive** — four categories + video (`IcThM_Jx8bY`)
6. `#directory-embed` — **Community Directory** Softr iframe
7. `#events` — **BSL Events & Community Video Archive** Softr iframe
8. `#role-models` — **See It. Believe It. Be It.** — overview video (`G2zSuR0IxWA`) followed by a centred role-model card grid. The card pattern: each `.role-card` holds a `.video-facade` thumbnail, a visible short tagline in `.role-card-body`, and a hidden `.role-card-bio` (the full text). Clicking the facade opens the lightbox with the bio populated in `.video-modal-details`. Currently only David is shipped (`k6CsKt1l9dU`); the grid uses `flex; justify-content: center` so any number of cards (1, 2, 3, …) lay out cleanly.
9. **Help Us Build DeafHive** — invitation + video (`rBCV5m3_C9U`)
10. Footer

All YouTube videos use the **facade pattern**: a `<button class="video-facade" data-id="...">` with a YouTube `hqdefault.jpg` thumbnail and play-button overlay. A delegated click handler at the bottom of the inline `<script>` swaps the facade for an autoplay iframe on click. This hides YouTube's pre-play title/avatar/Watch-on-YouTube clutter.

Bullets across the site use a plain `•` (navy, bold) — set via three `::before` rules: `.bullet-list li`, `.find-item`, `.archive-bullets li`. Keep them consistent if you add new lists.

## Architecture & decisions

This section explains the *why* behind the patterns in `index.html`. Read before changing video markup, modal behaviour, the hex background, or section layout — otherwise it's easy to undo a deliberate trade-off.

### 1. YouTube facade pattern (click-to-play)

Every video on the page is a `<button class="video-facade">` showing a static thumbnail + play button. Clicking it swaps the button for an autoplay iframe.

**Why**:
- Hides YouTube's pre-play branding (avatar, channel name, "Watch on YouTube"). With 6 videos on one page the unstyled iframes looked very cluttered.
- No YouTube JS / CSS / iframe loads until the user opts in — big first-paint perf win.
- Privacy-respecting: no YouTube tracking until the click.

**How**:
- Each facade carries `data-id` (YT video ID) and `data-title`.
- The `<img>` uses `https://i.ytimg.com/vi/<ID>/hqdefault.jpg`. We deliberately don't use `maxresdefault` — YouTube silently returns a 120×90 placeholder for videos that don't have it, and the placeholder has a 200 status so `onerror` doesn't fire. `hqdefault.jpg` is universal.
- The play icon is a single `<symbol id="yt-play">` defined once at the top of `<body>`. Each facade references it with `<svg><use href="#yt-play"/></svg>`. Saves ~5 KB vs. 8 inline copies.
- Hover/focus recolour uses CSS custom properties (`--yt-play-bg`, `--yt-play-bg-opacity`) on `.video-facade-play` — direct `path` selectors don't reach into the `<use>` shadow DOM, but CSS variables cascade in.
- A single document-level click handler (`document.addEventListener('click', …)`) catches any facade click, builds the iframe with `?autoplay=1&rel=0`, and `replaceWith`s the button. If the facade lives inside a `.role-card`, it opens the lightbox instead (with bio panel populated).

**To swap a video**: change `data-id`, `data-title`, the `<img src>`, and the `aria-label`. Nothing else.

### 2. Video lightbox modal

Used by role-model cards. Clicking the facade opens a centred modal containing the autoplay iframe **and** the person's name + full bio. Keeps the page text-light while still letting visitors read the full story.

**Markup** (at the bottom of `<body>`):
```
#video-modal
  .video-modal-content     (flex column, 50vw desktop / 90vw mobile)
    .video-modal-close     (×, positioned outside the frame)
    .video-modal-frame     (receives the iframe)
    .video-modal-details[hidden]
      .video-modal-name    (h3)
      .video-modal-bio     (paragraphs)
```

**Behaviour** (`openVideoModal(id, title, invoker)` in the IIFE):
- Builds the autoplay iframe and inserts into `.video-modal-frame`.
- Reads `invoker.closest('.role-card')`. If found, copies the role-card's `.role-card-name` text and the hidden `.role-card-bio` HTML into the details panel. If not (overview videos), hides the panel.
- Sets `inert` on every direct child of `<body>` except the modal. This is the focus trap — no need for keydown-based tab cycling.
- Remembers the invoker as `lastFocusedFacade` so close returns focus there.

**Close routes** (all wired): Esc key, × button, click on the backdrop (`event.target === videoModal`).

**Why this shape**:
- `inert` is the modern focus-lock primitive; works in every browser we care about and replaces 30+ lines of manual focus-trap code.
- Bio panel uses the `hidden` attribute, not a class — simpler and semantically correct.
- Details panel has `max-height: 35vh; overflow-y: auto` so the modal never exceeds the viewport even with a long bio.

### 3. Hex background pattern (static SVG, not generated)

The navy background carries a faint rotated hex-line pattern. **Was** JS-generated on every load + every resize (~500 `<polygon>` DOM nodes). **Now** a static inline SVG that the browser tiles itself.

**Geometry**:
- A pointy-top hex with side `s = 34` has width `√3·s ≈ 58.95` and vertical row-step `1.5·s = 51`.
- The `<pattern id="hex-pattern">` tile is `59 × 102` (one column wide, two row-steps tall — the vertical period because adjacent rows are offset half a column horizontally).
- Three polygons inside the tile:
  - centred at `(29.5, 17)` — top row hex
  - centred at `(0, 68)` — bottom row, straddles the left edge (left half wraps into the previous tile)
  - centred at `(59, 68)` — bottom row, straddles the right edge (right half wraps into the next tile)
- The browser tiles the pattern. We draw **3 polygons** that tessellate, not 500.

**Layer styling** (`#hex-layer`):
- `position: fixed; inset: -12vmax; z-index: -1; pointer-events: none`
- `transform: rotate(-8deg) scale(1.08)` — the `-12vmax` padding plus the scale means rotation never exposes a page edge.
- Polygons use `vector-effect: non-scaling-stroke` so the stroke stays 2.2px regardless of the 1.08 scale.
- Under `prefers-reduced-motion: reduce` the transform becomes `none` (still tiled, no rotation).

**If you change `HEX_SIDE`**: recompute the polygons. Tile width = `√3 · side`, height = `3 · side`. Hex polygon points are `(cx + side·cos θ, cy + side·sin θ)` for `θ ∈ {-30°, 30°, 90°, 150°, 210°, 270°}`.

### 4. Heading / Video / Text layout (inner sections)

The 4 inner sections (What is, Why, What you'll find, Help us build) all follow this DOM order:

```
.card
  h2.sec-title           ← spans full card width
  .two-col
    .vid-box.side-vid    ← video (left on desktop)
    .col-text            ← bullets / paragraphs (right on desktop)
```

**Mobile** (≤768px): `.two-col` becomes a column; sections stack as **heading → video → text** consistently.
**Desktop** (>768px): heading on top, video left + text right side-by-side.

**Why**: earlier the headings sat inside `.col-text`, which alternated position (some sections had col-text on the left, some on the right). On mobile this caused some sections to read "video → heading → text" — the heading was buried below the video, disorienting and bad for screen-reader navigation. Pulling h2 out anchors every section to its heading.

**The hero card is intentionally different** — keeps its h1 + tagline + paragraph + CTAs left, video right. Mobile stacks text-then-video. The hero is the page intro, not a content section.

### 5. Softr iframe caching note

Each Softr iframe (Community Directory, BSL Events Archive) is followed by a single italic line of grey text (`.embed-note`):
> "Some images may not display — content is cached upstream and refreshes automatically every couple of hours."

**Why this exact shape**: Softr pulls images from Airtable attachment URLs, which Airtable rotates every ~2 hours (signed URLs that expire). Softr caches those URLs server-side; once they expire, every cached URL 404s and images break inside the iframe. There is no Softr setting to fix this from our side — verified against the Softr community, it's a well-known unsolved issue. The proper fix lives upstream (Air CDN proxy, Cloudinary + Zapier sync, or hosting images in Softr's own asset library instead of Airtable).

**Why not a Refresh button**: an earlier version of this section had a "Refresh" button that called `iframe.src = iframe.src`. It looked like a fix but didn't actually work — Softr's data layer caches server-side, so the iframe reload re-fetches the same stale Airtable URLs. False hope. The honest note is shorter and doesn't mislead.

### 6. Accessibility decisions

| Concern | Decision |
|---|---|
| Page lang | `<html lang="en-GB">` (UK audience) |
| Keyboard skip | `.skip-link` at top of body — `href="#about"`, slides into view on focus, navy outline |
| Reduced motion | `@media (prefers-reduced-motion: reduce)` disables hex rotation, facade hover transforms, scroll-behavior:smooth. The JS `scrollSectionIntoView` also switches to `behavior:'auto'` when `matchMedia('(prefers-reduced-motion: reduce)').matches`. |
| Modal focus | `inert` on every body sibling of the modal while open; focus returned to invoking facade on close. |
| Modal close routes | Esc key, × button, backdrop click — all three implemented. |
| Decorative SVGs | `aria-hidden="true"` on the hex layer container and the play-button `<symbol>` block. |
| Video iframes | All have `title` attributes. `<source>` not yet — captions live on YouTube. |
| Softr iframes | Both have descriptive `title` attributes. |
| Form / contact | None on the site. If we add one, it needs explicit `<label for>`s and a visible focus state. |

### 7. Mobile breakpoint

One breakpoint, **`max-width: 768px`**, handled by a single `@media` block at the bottom of the CSS. What changes:
- Nav becomes a hamburger (`.nav-toggle` shown, `.nav-links` hidden until `.is-open`)
- Cards: `flex-direction: column` on `.hero-card`, `.two-col`, `.archive-card`
- `.hero-vid` / `.side-vid` / `.archive-vid`: `width: 100% !important`
- `.hero-text h1` drops to 2rem
- `.find-grid` collapses to 1 column
- `.role-card` takes full width
- `.video-modal-content` widens to 90vw

If the breakpoint moves, change it in this one place.

### 8. Audit outcomes — what each round fixed

The site went through three audit passes. Each fixed a real metric, so don't accidentally undo these:

**P0 — first-paint perf + SEO essentials** (commit `dc2e2a0`)
- `loading="lazy"` on both Softr iframes — biggest perf win; defers ~MB each of Softr/React runtime.
- Full meta set: description, Open Graph (later + image), Twitter card, canonical, theme-color, inline-SVG favicon.
- Preconnect for `i.ytimg.com`, `youtube-nocookie.com`, `fonts.gstatic.com`.
- All `href="#"` placeholders wired or removed (logo → #about, hero CTAs → real sections, footer Terms/Privacy gone).
- 5 unused CSS classes + duplicate `#hex-layer` rule deleted.

**P1 — DOM weight + a11y** (commit `71ed198`)
- Hex SVG: 486 polygon nodes → 3 polygons in a `<pattern>`; resize-rebuild JS deleted.
- 8 inline play SVGs → 1 `<symbol id="yt-play">` + 8 `<use>` references.
- Skip-to-content link.
- `prefers-reduced-motion` block.
- Modal `inert` focus lock.
- `<html lang="en-GB">`.
- Dead `.vid-box svg { opacity: 0.7 }` rule deleted.

**P2 — structured data + OG image** (commits `5bdd8a9`, `268a40a`)
- JSON-LD `Organization` block. Helps Google's Knowledge Panel + AI tools identify "DeafHive" as a named entity.
- 1200×630 OG image (`og.png`, rendered from `og.svg` via `rsvg-convert`). Consistent on-brand preview cards in every platform that unfurls links.

**Net effect**: 673 DOM nodes → 202; 8 inline play SVGs → 1 symbol; 2 eager iframes → lazy; ~50 lines of runtime JS removed.

## Colours and styling (CSS variables, near the top of the file)

```
--navy:     #2852b7   page background
--navy-dk:  #1f46aa
--blue-mid: #3f66c4
--yellow:   #f5c842   primary CTA
--yellow-dk:#d4a800
--white:    #ffffff   cards
--text:     #1a2f6e   headings
--body:     #333
```

## Run locally

```sh
python3 -m http.server 8000
# open http://localhost:8000/
```

In a Claude Code session with the preview tools, use `preview_start` with the `deafhive` launch config (already in `.claude/launch.json`). It serves the folder on port 8000.

## Deployment

The site is live on **GitHub Pages** at `https://jackobeans.github.io/deafhive.online/`. Pushes to `main` auto-deploy via the default Pages build (legacy/Jekyll, source `main:/`).

Custom domain `deafhive.online` is registered but not yet pointed at the host.

### Gotcha: Pages turns off when the repo goes private

Making the repo private (even briefly) **disables** GitHub Pages on the free tier. Flipping back to public does **not** automatically re-enable it. Symptoms: live URL returns 404, `gh api repos/JackoBeans/deafhive.online --jq .has_pages` shows `false`. Fix:

```sh
gh api -X POST repos/JackoBeans/deafhive.online/pages \
  -f 'source[branch]=main' -f 'source[path]=/'
```

Build kicks off; site is back in ~30–60 seconds.

## Open items / things to remember

- **Custom domain:** registered at IONOS, DNS currently served by LiveDNS (`ns1-3.livedns.co.uk`, NamesCo/123-Reg). Not yet pointed at GitHub Pages. Site is currently live on GitHub Pages at `https://jackobeans.github.io/deafhive.online/`.

  **The three canonical-URL strings already point to `https://deafhive.online/`** (canonical, og:url, JSON-LD url). This was done in anticipation of the DNS switchover. Until DNS resolves, those URLs reference a host that doesn't exist — practical impact is small (low crawl traffic on a new site) and avoids a follow-up commit once DNS is wired up.

  When DNS is wired up, also add the domain on the GitHub Pages side:
  ```sh
  gh api -X PUT /repos/JackoBeans/deafhive.online/pages -f cname=deafhive.online
  ```
  Then enforce HTTPS once GitHub finishes provisioning the Let's Encrypt cert.
- **Cathy role-model card removed** (commit `ecdc975` had it; was using David's video ID as a placeholder because the original URL was unavailable). To re-add another role model, copy the David `<article class="role-card">` block and swap the `data-id`, `data-title`, the thumbnail `<img src>`, the `aria-label`, the `.role-card-name`, the tagline, and the hidden `.role-card-bio`. The flex grid will lay out 1, 2 or more cards cleanly.
- **Footer links** (`Terms & Support`, `Privacy Policy`) are placeholder `href="#"`. No legal pages exist yet.
- **Hero CTAs** (`Explore DeafHive`, `Get Involved`) are placeholder `href="#"`. Targets undecided.
- **`#directory` anchor** is on the "What You Will Find" card; the nav points to `#directory-embed` (the actual iframe card). Likely fine, but worth noting if reorganising IDs.

## Working rules for Claude Code

1. **Edit `index.html` directly.** No splitting into partials or build steps without asking.
2. **Show the plan before writing.** Especially for layout-affecting CSS changes — a small change can shift the whole grid.
3. **Verify in the preview.** This site is visual; type-checking and tests can't catch layout regressions.
4. **Don't introduce new dependencies.** No CSS frameworks, no JS libraries, no bundlers.
5. **Keep the file structure flat.** One HTML file. Assets next to it.
6. **British English in copy when in doubt** (organise, programme, colour) — the existing copy is mixed; lean British.
7. **Match existing capitalisation.** "Deaf" (not "deaf") throughout — opposite of the Conversant rule.
8. **Commits:** imperative mood, one line. Example: `hero: swap to new welcome video`.
9. **Don't push to `main` for substantive changes without confirming with Mark** — small content fixes are OK to push directly.

## Reference

- GitHub: https://github.com/JackoBeans/deafhive.online
- Domain: https://deafhive.online (pending deploy)
- Softr (content source for the two iframes): https://leola31663.softr.app — Mark has the account
- YouTube videos: `Andrew Tobin` channel hosts the BSL videos

---

*Last updated: 2026-05-12*
