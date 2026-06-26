# Publishing Cat Snake to itch.io (WebGL)

## Prerequisites

1. **Unity WebGL module** — In Unity Hub, add **WebGL Build Support** to your editor (6000.4.10f1).
2. **Butler** — itch.io’s upload CLI.
   - Download from [itch.io/butler](https://itchio.itch.io/butler), unzip, and put `butler` (+ `7z.so`, `libc7zip.dylib`) in `~/bin`.
   - Or install via the [itch desktop app](https://itch.io/app) (bundled butler).
   - See [installing butler](https://itch.io/docs/butler/installing.html).
3. **itch.io API key** — [Account → API keys](https://itch.io/user/settings/api-keys), or run `butler login` once.

## itch.io page settings

Your page should be configured as:

- **Kind:** HTML
- **This file will be played in the browser:** checked on the `web` channel upload
- **Embed options → Viewport dimensions:** **1280 × 720** (must match the WebGL build)
- **Orientation:** landscape
- Optional: enable **Fullscreen button** on the embed

The WebGL build uses the `ItchEmbed` template: canvas fills the iframe with no Unity footer bar (the default template adds ~40px below the canvas, which causes cropping on itch).

Butler uploads replace the files on that page; you do not need to zip the build manually.

## One-time setup

```bash
cp itch.env.example itch.env
```

Edit `itch.env`:

```bash
ITCH_USER=your-itch-username
ITCH_GAME=your-game-slug
ITCH_CHANNEL=web
BUTLER_API_KEY=your_api_key
```

The Butler target is `username/game-slug:channel` and must match your itch page.

## Build only (Unity menu)

In the editor: **Cat Snake → WebGL → Build for itch.io**

Output: `Builds/WebGL/` (must contain `index.html`).

Test locally:

```bash
cd Builds/WebGL
python3 -m http.server 8080
```

Open `http://localhost:8080` in a browser.

## Build and publish

```bash
chmod +x publish-itch-webgl.sh   # once
./publish-itch-webgl.sh
```

This runs a release WebGL build, then `butler push` with the current git commit as the version tag.

## Troubleshooting

- **Unity not found** — Set `UNITY_PATH` in `itch.env` to your editor binary.
- **Butler auth failed** — Set `BUTLER_API_KEY` or run `butler login`.
- **Build fails** — Check `unity-webgl-build.log` in the project root.
- **Game won’t load on itch** — Confirm the page kind is HTML and the channel name matches `ITCH_CHANNEL`.
