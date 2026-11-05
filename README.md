# Horizon Offline Launcher (unofficial)

The **official [Horizon launcher](https://github.com/horizon-rust/horizon)** with one small
patch: it plays **without a Microsoft account**. Everything else stays official —
game versions are downloaded from Horizon's own signed releases, launcher sources come
from upstream `main`, and GitHub Actions builds the binary the same way Horizon's release
workflow does.

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

## Keep the patch (important)

The launcher checks the **official** Horizon repository for launcher updates. Until you disable
that, a new official release can replace this build — and bring the account gate back.
Set the no-self-update switch once:

**Windows:** `setx HORIZON_LAUNCHER_NO_SELF_UPDATE 1` (then restart the launcher)

**Linux:** put `export HORIZON_LAUNCHER_NO_SELF_UPDATE=1` in your shell profile.

Game updates (new Horizon Nightlies and releases of the game) are unaffected: they keep
coming from the official repository and are installed as usual.

## Building

`.github/workflows/release.yml` builds on GitHub Actions — `cargo build --release --locked`
in `launcher/`, with the version and tag handed to the build (`HORIZON_LAUNCHER_VERSION`,
`HORIZON_LAUNCHER_TAG`). A push of a `launcher@<version>` tag builds and publishes a Release;
* Releases → workflow run (artifacts, no release).

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
- 10 languages gained the offline strings; the README fork redirects, fork tag hacks
  and fork signing were **not** taken.

Not taken from the fork: its own release repository/signing key (game and launcher
downloads stay on `horizon-rust/horizon`), its 4-part version ordering, and its
self-update tweaks.

## License

MIT — the launcher is Horizon's work (see `LICENSE`, `THIRD_PARTY_NOTICES.md`);
see `NOTICE.txt` for attribution. Not affiliated with Mojang or Microsoft.
