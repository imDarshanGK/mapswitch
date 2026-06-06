# MapSwitch

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/YOUR_USERNAME/maps-deeplink)

> Share one map link. It opens in the **right app** on every phone.

Send a restaurant link to a friend. On iPhone → Apple Maps opens. On Android → Google Maps opens. On desktop → browser fallback. No app switching, no frustration.


- 🚫 Zero backend — pure static HTML + JS
- ⚙️ Config-driven — add platforms by editing `platforms.json`
- OS-aware — detects iOS, Android, or desktop automatically
- 🔒 Private — runs entirely in the browser, no tracking
- Web fallback — always works even if the app isn't installed

---

## How it works

### Generator (`/`)
1. Paste a Google Maps or Apple Maps URL
2. The page extracts the location ID using `platforms.json` regex extractors
3. You get a shareable smart link: `https://mapswitch.netlify.app/r/google-maps/{id}`

### Redirect (`/r/{platform}/{id}`)
1. Shows a loading UI while detecting the OS
2. **iOS** → launches `maps://` (Apple Maps)
3. **Android** → launches `geo:` intent (Google Maps)
4. **Web** → opens Google Maps in the browser
5. Manual buttons always visible as fallback

---

## Deploy to Netlify

### Option A — One-click
Click the **Deploy to Netlify** button above.

### Option B — CLI
```bash
npm i -g netlify-cli
netlify deploy --prod
```

### Option C — Drag & drop
Drag this folder to [app.netlify.com/drop](https://app.netlify.com/drop).

---

## Adding a platform

Open `platforms.json` and add a new entry:

```json
"my-maps-platform": {
  "name": "My Maps",
  "icon": "map",
  "color": "#ff0080",
  "domains": ["myplatform.com"],
  "extractors": ["myplatform\\.com/maps/([A-Za-z0-9]+)"],
  "idValidator": "^[A-Za-z0-9]+$",
  "deepLink": {
    "ios": "mymaps://open?id={id}",
    "android": "geo:0,0?q={id}",
    "fallback": "https://myplatform.com/maps/{id}"
  },
  "webLink": "https://myplatform.com/maps/{id}"
}
```

Then sync the inline fallback config:
```bash
node scripts/sync-inline-config.mjs
```

---

## Field reference

| Field | Required | Description |
|-------|----------|-------------|
| `name` | yes | Display name |
| `icon` | no | Emoji shown in the UI |
| `color` | no | Hex color for the platform tile |
| `domains` | yes | Allowed hostnames — security allow-list |
| `extractors` | yes | Regex patterns; first capture group = `id` |
| `idValidator` | no | Regex the extracted `id` must match |
| `deepLink.ios` | yes | Custom URL scheme for iPhone/iPad |
| `deepLink.android` | yes | `geo:` intent URI for Android |
| `deepLink.fallback` | yes | Web URL used on desktop |
| `webLink` | yes | Canonical web URL for the location |

---

## Local preview

```bash
python3 -m http.server 8080
# or
npx serve .
```

Open `http://localhost:8080`. The redirect routes (`/r/...`) require a static server that rewrites to `index.html` — the Python server won't handle them, but Netlify CLI will:

```bash
netlify dev
```

---

## Security & privacy

- **Allow-list** — only domains listed in `platforms.json` are accepted
- **ID validation** — extracted IDs are validated against `idValidator` regex before any redirect
- **No server** — all logic runs in the browser, no data leaves the client
- **No tracking** — zero analytics, zero third-party scripts

---

## Project layout

```
maps-deeplink/
├── index.html          # the entire app
├── platforms.json      # platform config (source of truth)
├── netlify.toml        # Netlify publish dir, rewrites, headers
├── _redirects          # Netlify rewrite fallback
├── scripts/
│   └── sync-inline-config.mjs   # syncs platforms.json into index.html
└── LICENSE
```

---

## Contributing

1. Fork & clone
2. Edit `platforms.json` to add a platform
3. Run `node scripts/sync-inline-config.mjs`
4. Test with `netlify dev`
5. Open a pull request

---

## License

MIT — free to use, modify, self-host, and redistribute. Keep the copyright notice.

---

*Inspired by the frustration of sending a Google Maps link to an iPhone user and watching it open in Safari.*
