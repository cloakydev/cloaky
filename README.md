# Cloaky

A desktop workbench that runs Claude Code, OpenCode, Codex, Kimi Code and Grok Build on [Venice](https://venice.ai) models with your own Venice API key.

**Cloaky is in public beta.** It is ready for daily work, and some edges are still rough. Report a problem in [Issues](https://github.com/cloakydev/cloaky/issues), or from the app: Settings › General › Help and legal › Report a problem, where Logs shows the newest log to attach. Ask questions in [Discussions](https://github.com/cloakydev/cloaky/discussions).

## Download

[Download the latest release](https://github.com/cloakydev/cloaky/releases/latest): `Cloaky-<version>-arm64.dmg` for Apple Silicon, `Cloaky-<version>-x64.dmg` for Intel. macOS 13 (Ventura) or later. There is no Linux or Windows build yet.

Builds are signed with a Developer ID certificate and notarized by Apple. To check a download before opening it:

    shasum -a 256 -c SHA256SUMS --ignore-missing      # SHA256SUMS is attached to every release
    spctl -a -vv /Applications/Cloaky.app             # expect: source=Notarized Developer ID

## Before your first session

- A Venice API key from an account with credit, from [venice.ai/settings/api](https://venice.ai/settings/api). Setup checks the key and the balance.
- At least one engine installed with its vendor's own command: Claude Code, OpenCode, Codex, Kimi Code or Grok Build. Setup shows which are installed and the command for the rest.

## Updates

The first and last steps of setup offer **Check for updates automatically**, ticked the first time and showing your saved choice after that; skipping or finishing setup saves what the box says. Change it, or check by hand, in Settings › General › This Mac. Your settings and sessions live in `~/.cloaky` and stay across updates.

## Privacy and safety

Cloaky sends no telemetry, analytics or crash reports, and runs no server of its own. [docs/PRIVACY.md](docs/PRIVACY.md) lists every host the app contacts and what it sends. Approvals are a review step, not a sandbox: read [SECURITY.md](SECURITY.md), which also says how to report a vulnerability privately.

## License

Free to download and use for personal and commercial work, under the [Cloaky License](LICENSE). The source is not published yet; this repository holds releases only.
