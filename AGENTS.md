# Nuxt bundle cheat sheet

A one-page reference on how a Nuxt bundle loads: the kinds of chunks Vite writes, where code ends up, what the HTML preloads, what waits for what, and how Static, SSR and SPA differ. It backs the work on prerendered URLs in [nuxt-bundle-analyzer](https://github.com/atinux/nuxt-bundle-analyzer), and it's deployed on Vercel so it can be opened whenever a bundle question comes up.

## Files

- `index.html`: the whole page. CSS, JavaScript and data are inline, and it makes no network requests. Vercel serves it as a static file, with no build step.
- `AGENTS.md`: this file.

## Facts on the page

- Every claim is either measured or labeled as illustrative. The playground timeline is illustrative and says so; the "Measured" runs, the tag counts and the Docus sizes are real.
- Before adding or changing a claim about what Nuxt or the browser does, check it on a real build and write down how. The checks so far used the nuxt-bundle-analyzer playground with Nuxt 4.6.3 and nitropack 2.13.5:
  - Tag lists: read every `/_nuxt/` `<script>` and `<link>` of the HTML and match each file to the client manifest (`.nuxt/dist/server/client.manifest.mjs`) to get its kind.
  - Static vs SSR: diff the tags of the prerendered file in `.output/public` against the SSR response of `node .output/server/index.mjs` for the same URL.
  - What waits: put a proxy in front of the server that delays one chunk by 3 s, click a button next to the component every 50 ms in Chromium, and record `app:mounted`, `app:suspense:resolve`, `page:finish` and `onNuxtReady` from a client plugin.
- Docus numbers are Brotli sizes. Route sizes and chunk counts come from the run stored for commit 7a2b5e1 on bundle.nuxt.dev; the `/en` URL size comes from its prerendered HTML.
- Wording: chunks a URL loads beyond its route are "lazy-loaded components the page rendered: its HTML preloads them, but the route's static imports don't reach them". Page, route and URL keep the meanings in the "Three names, three sizes" block.

## Editing

- Keep the page self-contained. Colors come from the CSS variables at the top of the `<style>` block, with a light and a dark set; add colors there, not inline.
- Data for the interactive parts lives in the `<script>`: `kinds`, `states` and `notes` for the playground, `modes` for the Static/SSR/SPA table, `runs` for the measured runs. Titles and counts are computed from that data, so update the data rather than the text.
- After a change, open the page at 1280 px and 390 px wide, in light and dark, and check that nothing overflows sideways and the console has no errors.
