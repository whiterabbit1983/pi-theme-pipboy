# pi-theme-pipboy

Pip-Boy theme for [Pi](https://pi.dev) — a Fallout-inspired terminal palette in phosphor green
and amber, packaged as a Pi package so anyone can install it with one command.

![Pip-Boy theme preview](https://raw.githubusercontent.com/whiterabbit1983/pi-theme-pipboy/main/assets/preview.svg)

## Install

```bash
pi install npm:pi-theme-pipboy
```

Then pick the theme:

- `/settings` → **Theme** → `pipboy`, or
- start a single session with `pi --use-theme pipboy`

To pair it with a light terminal, use `pipboy/<other-theme>` in the `theme` setting.

## Uninstall

```bash
pi remove npm:pi-theme-pipboy
```

## Replace the theme JSON

The theme lives in a single file: [`themes/pipboy.json`](themes/pipboy.json).

**To publish a new look**

1. Edit `themes/pipboy.json`.
2. Validate it against the published schema:
   ```bash
   npm run validate
   ```
3. Regenerate the preview image:
   ```bash
   npm run preview
   ```
4. Bump the version in `package.json`, then `npm publish`.

**To iterate locally without publishing**

```bash
pi install .          # installs from this directory
```

Then edit `themes/pipboy.json` and run `/reload` in Pi. Or, to skip the package entirely and
hot-reload instantly, copy the file straight into your agent directory:

```bash
cp themes/pipboy.json ~/.pi/agent/themes/pipboy.json
```

Pi hot-reloads the active user theme only from `~/.pi/agent/themes/<name>.json`, so edits to that
file apply without `/reload`. Changes anywhere else need `/reload`.

### Validating the theme

`npm run validate` checks the theme files against the rules Pi enforces — the required and optional
color roles, the reserved theme name, unknown properties, color literal formats, and `vars`
resolution including circular references.

```bash
npm run validate                                    # every themes/*.json
node scripts/validate-theme.mjs themes/pipboy.json  # specific files
node scripts/validate-theme.mjs --check-schema      # also compare with the published schema
```

The validator has no dependencies. `--check-schema` downloads
[`https://pi.dev/schemas/theme.schema.json`](https://pi.dev/schemas/theme.schema.json) and fails if
Pi has added or renamed color roles since this script was written.

### Regenerating the preview

`npm run preview` renders `assets/preview.svg` from the theme file — it is a mock Pi transcript
painted with the theme's own color roles, so it stays in sync with your palette.

```bash
npm run preview                          # themes/pipboy.json -> assets/preview.svg
node scripts/generate-preview.mjs --theme themes/other.json --out assets/other.svg
```

The script has no dependencies. If Pi is installed on the machine it borrows Pi's own color
parser, so `oklch()` and `okhsl()` values convert exactly the way Pi converts them; otherwise a
built-in converter is used. Text is laid out on a fixed character grid, so the preview renders
identically in every browser.

### Theme file rules

| Property | Required | Notes |
|---|---|---|
| `$schema` | no | Points at `https://pi.dev/schemas/theme.schema.json` for editor validation. |
| `name` | yes | Must be unique, cannot contain `/`, cannot be `system`. |
| `appearance` | no | `"dark"` or `"light"`. Detected from the colors when omitted. |
| `vars` | no | Reusable color values. Variables may reference other variables. |
| `colors` | yes | 51 required roles, 5 optional. Unknown keys are rejected. |
| `export` | no | Overrides page and panel backgrounds for HTML exports. |

Colors accept `#RGB`, `#RRGGBB`, `oklch(...)`, `okhsl(...)`, a `0`–`255` ANSI index, a `vars`
reference, or `""` for the terminal default. Missing variables and circular references make the
theme invalid.

Optional colors and their fallbacks: `scrollbarTrack` → `muted`, `scrollbarThumb` → `text`,
`searchMatchBg` → `selectedBg`, `searchMatchText` → `text`, `thinkingMax` → `thinkingXhigh`.

To ship more than one theme, drop additional JSON files into `themes/` — Pi discovers every
`*.json` there. Give each a distinct `name`, and the file name must match that name.

## Publish to npm

```bash
npm login
npm publish --access public
```

Publishing to a public scope (`npm publish --access public`) requires a matching scope on the
package name and `npm access public`.

## Appear in the Pi package gallery

The gallery at <https://pi.dev/packages> indexes npm packages that carry the `pi-package`
keyword. This package already declares it in `package.json`, so it is eligible as soon as it is
published.

Checklist:

1. **`pi-package` keyword** — already set.
2. **Published to npm under a public name** — `npm publish --access public`.
3. **A `pi` manifest in `package.json`** — already set to `{ "themes": ["./themes" ] }`. The
   gallery uses this to decide that the package is a *theme* package.
4. **Optional `pi.image`** — set to a preview URL. Absolute URLs are used as-is, so this package
   points at the raw GitHub preview, which resolves before the package reaches npm:
   ```json
   "pi": {
     "image": "https://raw.githubusercontent.com/whiterabbit1983/pi-theme-pipboy/main/assets/preview.svg",
     "themes": ["./themes"]
   }
   ```
   `pi.video` works the same way for a short screen recording.
5. **A `repository` field** — the gallery links the repo. It points at
   <https://github.com/whiterabbit1983/pi-theme-pipboy>.
6. **The package name must be unique on npm.** `pi-theme-pipboy` may already be taken — run
   `npm view pi-theme-pipboy` to check, and pick a scoped name such as `@you/pi-theme-pipboy`
   if it is.

The gallery refreshes automatically after publish; indexing usually takes a few minutes.

## License

MIT
