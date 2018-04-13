---
title: FAQ - How to customize Parity UI?
---

In dev mode:
- Fork `https://github.com/parity-js/shell` and make your modifications to the UI
- `npm start`
- Run node with --ui-no-validation
- Visit `localhost:3000`, the page should refresh on file changes.
- (optional) Commit your changes, and submit a PR!

In local prod mode:
- Fork `https://github.com/parity-js/shell` and make your modifications to the UI
- Push your changes, copy the latest commit reference
- Clone/Fork `https://github.com/paritytech/parity`, and in `./dapps/ui/Cargo.toml` modify the line `parity-ui-dev = { ... }` to point to your forked repo and commit reference
- Run `cargo update -p parity-ui` to update Cargo.lock
- `cargo build --release --no-default-features --features ui`
- Run parity, visit localhost:8180.

Dev mode should be used as much as possible, since it allows fast iterations. Local prod mode is only used when you want the UI changes to be bundled into your parity when `cargo build`ing it.
