# DeafHive

A central online space for the Deaf community, bringing together everything from local events to life-changing support.

- **Live site:** https://deafhive.online
- **Repo:** https://github.com/JackoBeans/deafhive.online

---

## What this is

DeafHive is a single-page information and inclusion site for the Deaf community. It pulls together:

- A welcome introduction in BSL with English captions
- **Community Directory** — Deaf groups, events, services and support (embedded from Softr)
- **BSL Events & Community Video Archive** (embedded from Softr)
- **See It. Believe It. Be It.** — Deaf role models
- **Help Us Build DeafHive** — an invitation to contribute, partner, or sponsor

## How it's built

- **One file** — `index.html`. No framework, no build step.
- **Static HTML, vanilla CSS, vanilla JS.** Custom CSS variables for the navy + yellow palette. `Raleway` from Google Fonts.
- **Hex pattern background** — a single inline `<svg><pattern>` definition tiled by the browser. No JS, no per-resize work.
- **YouTube embeds** — privacy-respecting `youtube-nocookie.com` iframes, lazy-loaded behind a click-to-play facade.
- **Softr embeds** — two `iframe` blocks for the Community Directory and Events Archive. Content is managed in Softr's native database; the site just renders the iframes.

## Run locally

```sh
# from the repo root
python3 -m http.server 8000
# open http://localhost:8000/
```

That's it. No `npm install`, no build step.

## Editing content

Everything lives in `index.html`. Sections, in order:

1. **Nav** — sticky header with About / Directory / Events / Role Models
2. **Hero** — Welcome video + tagline + CTAs
3. **What is DeafHive?** — three-point intro with video
4. **Why We Created DeafHive?** — context + video
5. **What You Will Find on DeafHive** — four categories + video
6. **Community Directory** — Softr iframe
7. **BSL Events & Community Video Archive** — Softr iframe
8. **See It. Believe It. Be It.** — overview video + role-model card (David)
9. **Help Us Build DeafHive** — invitation + video
10. **Footer**

To swap a video, find the `<button class="video-facade">` for that section and change the `data-id`, `data-title`, `<img src>` and `aria-label`. To change copy, edit the HTML directly.

## Deployment

Live on **GitHub Pages** at `https://deafhive.online/` (with `www.deafhive.online` redirecting). Pushes to `main` auto-deploy via the default Pages build. HTTPS is enforced.

## Project status

- ✅ Custom domain `deafhive.online` live with HTTPS
- ✅ All six BSL videos wired in (Welcome, What is, Why, What You Will Find, See It Believe It Be It, Help Us Build)
- ✅ David role-model card with bio-in-modal pattern (ready for more role models)
- ✅ Click-to-play facades — pre-play YouTube clutter hidden, page loads fast
- ✅ Lazy-loaded Softr iframes — first paint isn't blocked
- ✅ Softr embeds now backed by Softr's native database (no more Airtable rate-limit / expiring-image issues)
- ✅ Open Graph + Twitter card with on-brand 1200×630 image
- ✅ Accessibility: skip link, `prefers-reduced-motion`, modal focus lock, `lang="en-GB"`
