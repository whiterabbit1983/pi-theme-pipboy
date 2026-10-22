# pi-theme-pipboy

Pip-Boy theme for [Pi](https://pi.dev) — a Fallout-inspired terminal palette in phosphor green
and amber, packaged as a Pi package so anyone can install it with one command.

![Pip-Boy theme preview](https://cdn.jsdelivr.net/npm/pi-theme-pipboy/assets/preview.svg)

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
   npx --yes ajv-cli@5 validate -s https://pi.dev/schemas/theme.schema.json -d themes/pipboy.json
   ```
   (or point `-s` at a local copy: `curl -sS https://pi.dev/schemas/theme.schema.json -o theme.schema.json`)
3. Bump the version in `package.json`, then `npm publish`.

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
4. **Optional `pi.image`** — set to a preview URL. Absolute URLs are used as-is, so you can point
   at a raw GitHub URL once the repo is public:
   ```json
   "pi": {
     "image": "https://raw.githubusercontent.com/OWNER/pi-theme-pipboy/main/assets/preview.svg",
     "themes": ["./themes"]
   }
   ```
   `pi.video` works the same way for a short screen recording.
5. **A `repository` field** — the gallery links the repo. Replace `OWNER` in `package.json` with
   your GitHub user or org.
6. **The package name must be unique on npm.** `pi-theme-pipboy` may already be taken — run
   `npm view pi-theme-pipboy` to check, and pick a scoped name such as `@you/pi-theme-pipboy`
   if it is.

The gallery refreshes automatically after publish; indexing usually takes a few minutes.

## License

MIT
