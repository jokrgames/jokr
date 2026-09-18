# jokr — the Jokr Games website

A static site for two Android card games. No build step, no dependencies, no
JavaScript: plain HTML, one stylesheet, and images derived from the game repos.

Published with GitHub Pages at **https://jokrgames.github.io/jokr/** — a
*subpath*, not a domain root, which is why every `href` and `src` in the pages is
relative. The only absolute URLs are `rel=canonical` and the Open Graph tags,
which scrapers will not resolve otherwise.

## What is here

| File | |
|---|---|
| `index.html` | Hub — the studio and the two games |
| `jokr.html` | JOKR — Joker Kiske Paas? (live on Google Play) |
| `seepify.html` | Seepify (in development; nothing on it is a download) |
| `privacy-policy.html` | JOKR's policy — the URL in its Play listing |
| `seepify-privacy.html` | Seepify's policy, for its listing when it ships |
| `styles.css` | Shared styles for the three content pages |
| `404.html`, `sitemap.xml`, `.nojekyll` | |
| `assets/` | Derived images only — see below |

The two policy pages are **standalone on purpose**: each carries its own
`<style>` block and links no stylesheet, so either can be dropped into any host
or back into its game repo and still render. That is why the palette exists in
two places.

## Two things to keep in sync

1. **`privacy-policy.html` and the app repo's copy.** They were byte-identical
   and are not any more: this copy now has the real contact address where both
   had a `PRIVACY_CONTACT_EMAIL` placeholder, and its "Last updated" date moved
   to 19 September 2026 to match. **Pending:** make the same two changes in
   `../jokerkiskepaas/store/privacy-policy.html` and in
   `../jokerkiskepaas/docs/PRIVACY.md`, after which

   ```bash
   shasum -a 256 privacy-policy.html ../jokerkiskepaas/store/privacy-policy.html
   ```

   should print two identical hashes again, and should keep doing so.

2. **The palette.** The `:root` block at the top of `styles.css` is a verbatim
   copy of `privacy-policy.html` lines 18–45. New tokens are *appended* below it,
   never edited into it, so a diff of the two blocks stays empty. Change a
   colour in one and change it in the other.

## Regenerating the images

Nothing full-size is committed. Everything in `assets/` is derived from the game
repos with `sips`, which ships with macOS. From this directory:

```bash
J=../jokerkiskepaas/store
S=../seepify/app/web/icons

# JOKR screenshots -> 540x960 JPEG, ~60-90 KB each
for f in 02_target 03_table 04_reveal 05_curtain 06_gameover; do
  sips -Z 960 -s format jpeg -s formatOptions 65 "$J/screenshots/$f.png" \
       --out "assets/jokr/shots/$f.jpg"
done

# Icons and favicons
sips -Z 256 "$J/jokr_store_icon_512.png" --out assets/jokr/icon-256.png
sips -Z 256 "$S/Icon-512.png"            --out assets/seepify/icon-256.png
sips -Z 180 "$J/jokr_store_icon_512.png" --out favicon-180.png
sips -Z 32  "$J/jokr_store_icon_512.png" --out favicon-32.png

# Open Graph cards: pad the icon to 1200x630, THEN convert. Doing both in one
# sips call writes PNG bytes into a .jpg file.
sips -Z 520 "$J/jokr_store_icon_512.png" -p 630 1200 --padColor 1E1B4B --out /tmp/pad-jokr.png
sips -Z 520 "$S/Icon-512.png"            -p 630 1200 --padColor 061713 --out /tmp/pad-seep.png
sips -s format jpeg -s formatOptions 72 /tmp/pad-jokr.png --out assets/og/og-jokr.jpg
sips -s format jpeg -s formatOptions 72 /tmp/pad-seep.png --out assets/og/og-seepify.jpg
cp assets/og/og-jokr.jpg assets/og/og-home.jpg
```

Deliberately **not** used:

- `store/screenshots/01_home.png` — it shows a "Play Online" row, and online
  play is switched off in the released app (see below).
- `store/jokr_feature_graphic_1024x500.png` and
  `store/jokr_feature_banner_4096x2304.jpg` — the wordmark on them reads
  "JOKER · Play • Match • Win", which is not the brand or the tagline the site
  uses, and `store/README.md` (D-015) records their rights position as
  unconfirmed. The heroes are CSS instead.

Seepify has no artwork to borrow at all — see `assets/seepify/shots/README.md`.

## Rules the copy follows

- **Online play is not advertised.** `kOnlinePlayEnabled` is `false` in the
  shipped app and no server is hosted for it, so claiming it would be a
  misleading store listing. `jokr.html` mentions it once, in the FAQ, to say it
  is not switched on.
- **The word "free" does not appear.** Play shows the price itself and
  promotional words in metadata are against its policy. The copy says "no ads,
  no in-app purchases" instead.
- **Seepify has no download link of any kind** until there is something to
  download.

## Working on it locally

Serve the **parent** directory, so the `/jokr/` subpath is reproduced exactly —
an accidental leading-slash path then breaks here the same way it would in
production, instead of silently working:

```bash
cd .. && python3 -m http.server 8000
# open http://localhost:8000/jokr/
```

Checks worth re-running after an edit:

```bash
grep -n 'href="/\|src="/\|url(/' *.html styles.css   # must print nothing
grep -rniE '\bfree\b' *.html                          # must print nothing
du -ak assets | sort -n                               # index <= 150 KB, jokr <= 250 KB
```

## Publishing

GitHub Pages is configured on github.com (Settings → Pages → branch `main`,
folder `/`), not in this tree. There is no `CNAME`: if a domain is added later,
the base URL is hard-coded in the `<head>` of `index.html`, `jokr.html` and
`seepify.html`, and in `sitemap.xml` — four files, find-and-replace.
