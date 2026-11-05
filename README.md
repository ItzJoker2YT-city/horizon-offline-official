# Horizon Offline Launcher (unofficial)

The **official [Horizon launcher](https://github.com/horizon-rust/horizon)** with one small
patch: it plays **without a Microsoft account**. Everything else stays official — game
versions are downloaded from Horizon's own signed releases, and GitHub Actions builds the
binary the same way Horizon's release workflow does. When Horizon ships a new launcher
release, an automated workflow here re-applies the patch onto its sources, builds and
publishes the matching offline release, and this launcher updates itself to it.

You can play with an offline name, with a Minecraft client jar linked from any launcher
(the official launcher, Prism, Modrinth App, SKLauncher, …), or with a folder from one.

## Downloads

Get the newest Release:

| Platform | File | What it is |
| --- | --- | --- |
| Windows | `horizon-launcher-windows-x64.zip` | Portable launcher; unzip and run `horizon-launcher.exe` |
| Windows | `horizon-launcher-setup.exe` | The same launcher as a per-user installer (Start menu entry, uninstaller) |
| Linux | `horizon-launcher-linux-x86_64.tar.gz` | Portable launcher |

`SHA256SUMS` lists every file's sha256.

## Updates (the patch keeps itself)

From 0.2.42 on, the launcher updates **itself from this repository**: clicking Update
installs the offline-patched build again, never the official one. An automated workflow
watches the official Horizon repository every 30 minutes, re-applies the offline patch to
each new `launcher@` release, builds it with Actions, signs it, and publishes it here.

Game updates (new Horizon Nightlies and releases of the game) are unaffected: they keep
coming from Horizon's own repository, checked against Horizon's signatures as before.

Only an install of the plain 0.2.41 (or of the official launcher itself) still reads the
official feed: install 0.2.42 here manually once to switch. `HORIZON_LAUNCHER_NO_SELF_UPDATE=1`
still works — since 0.2.42 it just means "never update myself at all".

## Building

`.github/workflows/build.yml` builds on GitHub Actions — `cargo build --release --locked`
in `launcher/`, with the version and tag handed to the build (`HORIZON_LAUNCHER_VERSION`,
`HORIZON_LAUNCHER_TAG`) — and, when asked to, signs the assets and publishes the Release:

- `release.yml`: a push of a `launcher@<version>` tag builds and publishes that Release;
  *Actions → release → Run workflow* builds only (artifacts, no release).
- `sync.yml`: every 30 minutes it checks the official repository for a new `launcher@`
  release; when found, it re-applies the offline patch onto its sources, pushes them and
  the tag here, and publishes the signed release — that is how new versions arrive.

### What the patch changes (and does not)

Changes on top of upstream `main` (three-way merged, patch extracted from
[gerdaroot/Horizon-Offline-Launcher](https://github.com/gerdaroot/Horizon-Offline-Launcher)):

- `account.rs`: `GATE_OPEN = true` — the owner gate never blocks an offline name.
- `main.rs` / `owner_page.rs`: the owner page is skipped for offline play; a crash report
  helper writes `launcher-crash.txt` (the window title says "Horizon Offline …").
- `backend.rs`: without an owning sign-in, Mojang's download falls back to your own
  install or a chosen client jar (`Settings.minecraft_jar`).
- `sources.rs`: prepare a version from a client jar file (`from_jar`), sha1-pinned.
- `cli.rs`: `--minecraft-jar` setting and the jar wording in `play/prepare`.
- `backend.rs`/`signed.rs`/`github.rs`: the launcher's self-update feed reads this
  repository (`LAUNCHER_UPDATES_REPO`) instead of Horizon's, while game versions, downloads
  and Nightlies stay on `horizon-rust/horizon`. Releases here are signed the official way
  (`SHA256SUMS` + `SHA256SUMS.sig`) with this project's own key, appended to
  `crates/horizon-release-sign/release-keys.txt` next to Horizon's.
- 10 languages gained the offline strings; the README fork redirects, fork tag hacks
  and fork signing were **not** taken.

Not taken from the fork: its 4-part version ordering and its own release-repository
tweaks beyond the update redirect (game downloads stay on `horizon-rust/horizon`; release
signing is Horizon's mechanism with one extra key for this repository's releases).

## License

MIT — the launcher is Horizon's work (see `LICENSE`, `THIRD_PARTY_NOTICES.md`);
see `NOTICE.txt` for attribution. Not affiliated with Mojang or Microsoft.
