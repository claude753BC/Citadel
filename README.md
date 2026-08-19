# Citadel — desktop build

This folder is a ready-to-build Tauri wrapper around the Citadel web app.
Your existing timer, WOOP log, to-do ledger, quotes, and soundscapes are
already ported into `dist/index.html` with local-file storage swapped
in for the browser-artifact storage — no other logic changed.

## One-time setup (first time only)

```
# Rust toolchain (Tauri's native shell is built with Rust)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Xcode command line tools (needed to compile on macOS)
xcode-select --install
```

## Build steps

```
cd citadel-tauri
npm install

# Generate the full icon set (all sizes/formats) from the source artwork
npm run tauri icon icon-source.png

# Run it locally in a dev window to check everything looks right
npm run tauri dev

# Produce the actual .app and .dmg you can drag into Applications
npm run tauri build
```

The finished app and installer will land in:
`src-tauri/target/release/bundle/macos/Citadel.app`
`src-tauri/target/release/bundle/dmg/Citadel_0.1.0_aarch64.dmg`

## Notes

- The app opens as a fixed-ish 480×920 window — tweak `windows` in
  `src-tauri/tauri.conf.json` if you'd rather it be resizable to a
  different default, always-on-top, or start minimized to the tray.
- Settings, session stats, the WOOP log, and to-dos now persist via
  `localStorage`, which is written to a real file on disk inside the
  app's own data directory — separate from your browser entirely.
- Soundscape links still open in your default browser (that's normal
  and correct — the app itself doesn't play audio).
- The app isn't code-signed or notarized, so on first launch macOS
  Gatekeeper may ask you to confirm you trust it (right-click → Open).
