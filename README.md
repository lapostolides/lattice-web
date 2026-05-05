# Lattice Landing Page

Single-page brochure site for the Lattice project. Zero build, no npm, no bundler:
just static HTML + Tailwind via CDN + a few mp4 clips + a QR code.

## Quick start (local preview)

```bash
cd website
open index.html        # macOS
# or: xdg-open index.html  (Linux), start index.html (Windows)
```

That's it. No server needed. Note: some browsers block video autoplay on
`file://` until you interact with the page once. That is browser policy,
not a bug.

## Deploy to GitHub Pages

1. Push this `website/` directory to `main`.
2. In GitHub: **Settings &rarr; Pages**.
3. **Source:** "Deploy from a branch".
4. **Branch:** `main` and **folder:** `/website`.
5. Click **Save**.
6. Wait ~60 seconds for the first build. The deployed URL will follow the
   pattern `https://<gh-username>.github.io/<repo-name>/`. For this repo
   the expected URL is:

   ```
   https://lapostolides3.github.io/lattice/
   ```

7. The `.nojekyll` file in this folder disables Jekyll processing on
   GitHub Pages so the asset paths pass through verbatim.

## Hooking up the email form (Formspree)

The form posts to `https://formspree.io/f/YOUR_FORM_ID`. Replace `YOUR_FORM_ID`
with a real form ID:

1. Sign up at <https://formspree.io> (free tier is fine).
2. Create a new form. Set the destination email to `lapostolides3@gmail.com`.
3. Copy the form ID Formspree gives you (looks like `xayweqkv` or similar).
4. In `index.html`, search for `YOUR_FORM_ID` and replace it.
5. Submit a test entry from the deployed page to confirm delivery.

A `mailto:lapostolides3@gmail.com` fallback is included immediately below the
form for users who prefer email or have JS / network restrictions.

## Regenerating the QR code

The QR code currently encodes `https://lapostolides3.github.io/lattice/`.
**Once you confirm the actual deployed URL, regenerate the QR.**

### Option A &mdash; `qrencode` (preferred, small + crisp PNG)

```bash
brew install qrencode
qrencode -o website/qr.png -s 10 -m 2 'https://<your-final-url>/'
```

### Option B &mdash; Python (no Homebrew needed)

```bash
pip install 'qrcode[pil]'
python3 -c "import qrcode; qrcode.make('https://<your-final-url>/').save('website/qr.png')"
```

After regenerating, commit `website/qr.png` and push. Note: the version of
the QR currently committed was generated with the Python `qrcode` library
(option B) because `qrencode` was not installed during the initial build.

## Video swaps

The page references five `.mp4` files under `assets/videos/`. They were
copied verbatim (or, in one case, re-encoded) from the `demo/` directory:

| Filename                          | Source                                                                        | Size    |
| --------------------------------- | ----------------------------------------------------------------------------- | ------- |
| `hero.mp4`                        | `demo/blender_localization_viz/output/3d_loc_left_half_looped.mp4`            | ~4.6 MB |
| `localization.mp4`                | `demo/blender_localization_viz/output/localization_side_by_side_titled_looped.mp4` | ~10 MB |
| `people_counting.mp4`             | `demo/blender_people_counting_viz/output/people_counting_looped.mp4`          | ~6.3 MB |
| `physical_understanding.mp4`      | `demo/physical_understanding_viz/output/scene.mp4` (non-looped &mdash; the `loop` HTML attr handles repeat) | ~2.5 MB |
| `sensor_search.mp4`               | `demo/sensor_search_viz/output/sensor_search.mp4` &mdash; **re-encoded** to fit budget | ~600 KB |

To swap a video, drop a new `.mp4` at the same path and commit. The cards
in `index.html` reference the filename only, no other code changes needed.

### Re-encoding sensor_search

The original `sensor_search.mp4` is ~18 MB. To re-encode at the same setting
used here (540p, CRF 30, no audio):

```bash
ffmpeg -i demo/sensor_search_viz/output/sensor_search.mp4 \
  -vf "scale=-2:540" -c:v libx264 -crf 30 -preset slow -an \
  website/assets/videos/sensor_search.mp4
```

If you want a higher-quality re-encode for the showcase card, drop `-crf` to
26 and check the resulting size with `du -h`. Stay under ~5 MB to keep the
total payload under GitHub Pages' soft 1 GB repo limit (and to keep the
page snappy on conference Wi-Fi).

## Total payload check

After any video swap, verify total size:

```bash
du -sh website/
```

Target: **under ~30 MB**.

## TODO markers

Search the source for `TODO` to find every placeholder that must be replaced
before launch:

```bash
grep -n TODO website/index.html
```

As of initial build, the TODOs are:

1. `YOUR_FORM_ID` &mdash; Formspree form ID (in `<form action=...>`).
2. GitHub link &mdash; `href="#"` on the footer link should point to the public repo URL.

## Out of scope

The following are intentionally not part of this page:

- Any backend / live agent integration. This is a brochure, not the app.
- Web analytics (Plausible, GA, etc.).
- Favicons, Apple touch icons, social share images (`og:image`, Twitter cards).
- A blog, docs site, or marketing CMS.
- A custom domain. (To wire one up later: add a `CNAME` file to this directory
  with the bare domain, configure DNS, and toggle "Enforce HTTPS" in Pages
  settings once Let's Encrypt provisions.)

## File map

```
website/
  index.html               # the page (semantic HTML, Tailwind via CDN)
  qr.png                   # QR code, encodes the deployed URL
  .nojekyll                # disables Jekyll processing on GitHub Pages
  README.md                # this file
  assets/
    videos/
      hero.mp4
      localization.mp4
      people_counting.mp4
      physical_understanding.mp4
      sensor_search.mp4
```
