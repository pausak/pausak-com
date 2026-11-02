# CLAUDE.md

Personal site for Aleks Pausak (pausak.com). A minimal dark page with a cursor-reactive dot grid, a link list with single-key shortcuts and a ⌘K/Ctrl+K command palette.

## Structure

- Everything (HTML, CSS, JS) lives in `index.html`. No framework or runtime dependencies; Vite is only the dev server/bundler.
- `npm run dev` serves on http://localhost:5173/, `npm run build` outputs to `dist/`.
- `assets/images/logo.png`: original logo (bright green, black "P", extra transparent space at the bottom). Kept as the source; not used by the page.
- `assets/images/favicon.png`: generated from `logo.png` (tinted to the accent, cropped and centred, 256×256). Used for both the favicon and the footer logo.

## Changing things

### Links
Edit the `links` array in the `<script>`. Each entry: `name`, `handle`, `key` (single-key shortcut), `icon` (a key in the `icons` object, which holds inline SVG paths), `url`. The list, the palette and the shortcuts all derive from this array.
- Keep the shortcut list in `README.md` in sync.
- `mailto:` links open in the same tab; everything else opens in a new tab.

### Accent colour
The accent (`#7cf5c4`) is hard-coded in three places. Change all of them together:
1. `--accent` CSS variable in `:root`.
2. The `rgba(124, 245, 196, …)` dot colour in the canvas `draw()` function.
3. `assets/images/favicon.png`. Regenerate it from `logo.png`, putting the new accent's RGB divided by 255 as the multipliers:

```sh
python3 -I -c "
from PIL import Image
im = Image.open('assets/images/logo.png').convert('RGBA')
r, g, b = 0.486, 0.961, 0.769  # accent RGB / 255
im.putdata([(round(r*G), round(g*G), round(b*G), a) for _, G, _, a in im.getdata()])
c = im.crop(im.getbbox()); w, h = c.size; side = max(w, h) + 16
out = Image.new('RGBA', (side, side), (0, 0, 0, 0)); out.paste(c, ((side - w)//2, (side - h)//2))
out.resize((256, 256), Image.LANCZOS).save('assets/images/favicon.png', optimize=True)
"
```

Don't tint the footer logo with a CSS/SVG filter. The image is already tinted, so a filter would tint it twice.

### Background colour
`--bg` in `:root` and `<meta name="theme-color">`. The non-accent dot colour (`rgba(237, 237, 239, …)`, matching `--fg`) is also hard-coded in `draw()`.

### Dot grid
`SPACING` (gap between dots) and `RADIUS` (cursor influence radius) sit at the top of the dot grid section. The grid renders statically when `prefers-reduced-motion` is set; keep it that way.

## Conventions

- **Keyboard shortcuts:** ⌘K and Ctrl+K both work on every platform. The footer label switches to "Ctrl+K" off Apple devices via `navigator.platform`. Key hints and the palette button are hidden on touch-only devices (`hover: none`).
- **Fonts:** system font stacks only (no web fonts), to keep the page dependency-free.
- **Git:** the user commits directly to `main` with short, sentence-case messages. Only commit or push when asked. The GitHub repo has been renamed to `pausak/pausak-com`; the local remote still uses the old `pausak.com` URL, which redirects.

## Possible future changes

- Swap `navigator.platform` (deprecated but still supported) for `navigator.userAgentData.platform` with a fallback.
- Point the `origin` remote at `https://github.com/pausak/pausak-com.git`.
- Delete the merged `redesign` branch (local and remote).
- Light mode, or a different accent (see above for every place it lives).
