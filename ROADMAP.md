# ROADMAP

Local reference checkout of upstream Stylus (openstyles/stylus) used as a comparison and parity target for StyleKit and StyleCraft. Not a fork intended for publication.

## Planned Work (reference use)

### Parity tracking
- Diff upstream `src/js/` every release to catch API changes in the popup / editor / options
- Mirror upstream's UserCSS preprocessor pipeline (`stylus`, `less`, custom variables) as a reference implementation in StyleKit
- Track the `@-moz-document` evaluator since StyleKit needs the same matching semantics for imported styles
- Follow `content/apply.js` changes — the way Stylus injects CSS at `document_start` is the canonical anti-FOUC pattern
- Watch for Firefox manifest changes (Stylus still ships MV2 and MV3 side-by-side)

### Features worth borrowing
- Stylus' inline search in the popup (style gallery search from the toolbar) — UX model for StyleKit's Find
- Live-reload against an external editor (file-watcher pattern documented in upstream wiki)
- Stylus' per-style configuration UI for `@var` UserCSS variables
- WebDAV/Dropbox/GDrive/OneDrive sync plumbing — StyleKit is Drive + Gist only, WebDAV is a frequent request
- CSSLint-mod rule set tuned for modern CSS — StyleKit could adopt the same ruleset to reduce false positives

### Sync notes
- No source modifications expected; this checkout is pinned and updated via `git pull` from upstream
- Any local experiments should be in a branch, never committed to `main` of this mirror

## Competitive Research

- **openstyles/stylus** — The reference. Keep pinned to a recent stable tag; treat `main` as read-only
- **Stylish (closed)** — Historical predecessor sold to analytics company; cautionary tale that lives in Stylus' README
- **Dark Reader** — Dynamic style generation, complementary to Stylus' static styles
- **UserStyles.world** — Community style registry Stylus fetches from; StyleKit uses the same endpoints

## Nice-to-Haves

- Annotation overlay (local-only) marking which Stylus files StyleKit has already ported and which diverge intentionally
- Automation that diffs upstream's `manifest.json` permissions against StyleKit's and flags drift
- A small script that extracts Stylus' CSS parser for isolated benchmarking against StyleKit's Monaco-lint path
- Repo-level README note that this is a reference mirror, not a shippable fork

## Open-Source Research (Round 2)

### Related OSS Projects
- https://github.com/openstyles/stylus — the reference itself (this repo); upstream active development; MV3 migration effort is the bellwether for the whole userstyles ecosystem
- https://github.com/openstyles/stylus/wiki/Stylish-alternatives — upstream-maintained list of adjacent tools worth diffing against StyleKit/StyleCraft
- https://github.com/ankit/stylebot — upstream Stylebot, the base our StyleKit fork comes from; direct feature parity target
- https://github.com/darkreader/darkreader — Dark Reader; orthogonal but overlapping category (adaptive inversion vs authored styles); informs StyleKit's "Readability mode"
- https://github.com/xthezealot/xstyle — xStyle, useful parity reference for legacy userstyles.org-era workflows
- https://github.com/firefoxcss-store/style-system — Firefox-CSS community theming approach; informs the browser-chrome side of theming (out of StyleKit's scope but worth tracking)
- https://userstyles.world/ — USW gallery + public API; canonical sync/discovery backend for UserCSS in the post-USO era
- https://uso.kkx.one/ — USO archive mirror; still the largest historical userstyles corpus

### Features to Borrow (parity targets for StyleKit / StyleCraft)
- Multi-cloud sync pluggability (Stylus' Dropbox / GDrive / OneDrive / WebDAV) — abstract as a `SyncProvider` interface to add WebDAV cheaply
- UserCSS with `@-moz-document` scoping (Stylus) — StyleKit should both author and consume UserCSS fluently
- Live-reload against an external editor (Stylus) — matches SASS/SCSS authoring workflows already common among our power users
- Stylelint + CSSLint-mod inline diagnostics (Stylus) — StyleKit already has Monaco; add linting with real-time inline errors
- JSON backup format compatible with Stylus + xStyle — bidirectional import/export between our fork and upstream without data loss
- ~10kB content script / ~1ms inject budget (Stylus) — benchmark StyleKit's CS against this, regress-on-every-PR in CI

### Patterns & Architectures Worth Studying
- MV3 migration approach — Stylus is blocked on Chromium MV3 (upstream issue #1430); StyleKit is on MV3. Document the patterns that made it possible (chrome.storage for hot state, chrome.alarms for wake-ups, no long-lived globals, no eval) so our fork stays ahead
- Content-script injection timing + size discipline — CSS-string injection at `document_start`, minimal JS, no blocking work; shipped as measured perf budget rather than folklore
- Linter integration (Stylelint + CSSLint-mod) as a pluggable pipeline — StyleKit's Monaco should expose the same hook points so community linters can plug in
- Sync-provider abstraction boundary — Stylus' multi-cloud support lives behind a tiny interface; adopt the pattern so StyleKit can grow providers without leaking into the core store
