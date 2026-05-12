# DeafHive

A central online space for the Deaf community, bringing together everything from local events to life-changing support.

- **Live site:** https://deafhive.online *(domain registered, deploy pending)*
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
- **Hex pattern background** — generated client-side as inline SVG (see the script at the bottom of the HTML).
- **YouTube embeds** — privacy-respecting `youtube-nocookie.com` iframes.
- **Softr embeds** — two `iframe` blocks for the Community Directory and Events Archive. Content is managed on Softr; the site just renders the iframes.

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
5. **What You Will Find on DeafHive** — four categories
6. **Community Directory** — Softr iframe
7. **BSL Events & Community Video Archive** — Softr iframe
8. **See It. Believe It. Be It.** — Role Models grid (placeholder thumbnails for now)
9. **Help Us Build DeafHive** — invitation + video
10. **Footer**

To swap a video, find the `<iframe>` for that section and change the YouTube ID in the `src` URL. To change copy, edit the HTML directly.

## Deployment

Planned: **Cloudflare Pages** connected to this repo, with `deafhive.online` as the custom domain. Pushes to `main` auto-deploy. *(Not wired up yet.)*

## Project status

- ✅ Hero, What is, Why We Created, Help Us Build — BSL videos wired in
- 🟡 Role Models grid — still placeholder thumbnails; awaiting individual videos
- 🟡 Custom domain + Cloudflare Pages — not yet connected
- 🟡 One unused video (`lalZkbr28n4` — second "Why we created DeafHive?") — pending decision on placement
