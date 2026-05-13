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
index.html       ← the entire site (HTML, embedded CSS, embedded JS)
README.md           ← public, on GitHub
CLAUDE.md           ← this file
.gitignore
.claude/launch.json ← local dev preview config (gitignored)
```

There are no other files. If a new asset is needed (image, font, etc.), put it next to `index.html` and reference it relatively.

## Sections in `index.html` (in order)

1. Sticky `<nav>` with logo + 4 links + mobile hamburger
2. `#about` — Hero with **Welcome to DeafHive** video (`_raNeUTdE6Q`)
3. **What is DeafHive?** — three bullets + video (`TAG5JM7zhTU`)
4. **Why We Created DeafHive?** — three bullets + video (`c29KSuD2iQc`)
5. **What You Will Find on DeafHive** — four categories + video (`IcThM_Jx8bY`)
6. `#directory-embed` — **Community Directory** Softr iframe
7. `#events` — **BSL Events & Community Video Archive** Softr iframe
8. `#role-models` — **See It. Believe It. Be It.** — overview video (`G2zSuR0IxWA`) followed by a 2-card grid of individual role models. Each `.role-card` holds a `.video-facade` thumbnail, a visible short tagline in `.role-card-body`, and a hidden `.role-card-bio` (the full text). Clicking the facade opens the lightbox with the bio populated in `.video-modal-details`.
9. **Help Us Build DeafHive** — invitation + video (`rBCV5m3_C9U`)
10. Footer

All YouTube videos use the **facade pattern**: a `<button class="video-facade" data-id="...">` with a YouTube `hqdefault.jpg` thumbnail and play-button overlay. A delegated click handler at the bottom of the inline `<script>` swaps the facade for an autoplay iframe on click. This hides YouTube's pre-play title/avatar/Watch-on-YouTube clutter.

Bullets across the site use a plain `•` (navy, bold) — set via three `::before` rules: `.bullet-list li`, `.find-item`, `.archive-bullets li`. Keep them consistent if you add new lists.

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

Not yet wired. Plan: **Cloudflare Pages** connected to the GitHub repo at https://github.com/JackoBeans/deafhive.online, with `deafhive.online` as the custom domain. Pushes to `main` auto-deploy.

Until that's set up, the site only runs locally.

## Open items / things to remember

- **Custom domain:** registered but not pointed at a host yet. Site is live on GitHub Pages at `https://jackobeans.github.io/deafhive.online/`.
- **Cathy role-model video** currently re-uses David's video ID (`k6CsKt1l9dU`) as a placeholder — the original URL Mark sent (`fNecBX0QxTI`) was unavailable. Swap to the real ID when Mark provides it.
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
