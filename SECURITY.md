# Security policy

## Reporting a vulnerability

Report vulnerabilities privately. The preferred channel is GitHub's private vulnerability reporting: open [Report a vulnerability](https://github.com/cloakydev/cloaky/security/advisories/new) on the repository's Security tab. If you cannot use GitHub, email hello@cloaky.dev.

Include the Cloaky version, your operating system, the engine involved and the steps to reproduce. Leave out real API keys, private conversations and unredacted logs.

We acknowledge a report within 7 days and aim to release a fix or publish an advisory within 90 days. Please do not disclose the issue publicly until a fix is released. We credit reporters in the advisory and the release notes unless they ask us not to.

## Supported versions

Only the latest release receives security fixes.

## Scope

In scope:

- the desktop app
- the local server
- the engine integration in this repository: how Cloaky installs, configures, launches and talks to each engine

Out of scope:

- an attacker who already controls your user account on the machine, and so can read Cloaky's files and run anything Cloaky can
- vulnerabilities in the engines' own vendor code (Claude Code, OpenCode, Codex, Kimi Code, Grok Build) or in Venice's service; report those to the vendor, unless Cloaky's integration is what makes them exploitable
- an action an agent takes that you approved, or that your chosen permission mode allowed without asking

## Threat model

Cloaky treats these as hostile: a page or file opened in the in-app browser; the files, web pages and tool results an agent reads and the files it writes; a repository opened as a project; a program's output in the terminal; and anyone able to change a download or an update between our build machines and your install. Code already running as your user is out of scope, as above.

- **Approvals are a review step, not a sandbox.** By default an engine edits files and runs commands on the host with your user's permissions. The permission mode decides which actions ask first; once an action is approved, or allowed by the mode, nothing contains it. Two settings under Settings → General, both off by default, narrow this: container isolation runs the session in a local container that confines files to the project's folders but leaves the network unrestricted, and the command sandbox runs shell commands inside the engine's OS-level sandbox with its default policy, without approval cards. Open untrusted projects in a disposable account or virtual machine.
- **Prompt injection.** Files, web pages and tool results that an agent reads are untrusted input. They can carry instructions that try to steer the agent toward an action you would not approve, which is what the approval step is for.
- **The local server** binds 127.0.0.1 only and rejects requests whose Host or Origin is not the app's own. A packaged build also requires a secret on every API request and socket: a new one is made each time the server starts. The server writes the secret and its port to `~/.cloaky/local-auth.json` with 0600 permissions, so that a desktop shell can attach to a server it did not start. Another user on the machine cannot read that file, but any program running as your user can, and with the secret it can drive sessions as the app does; that is the out-of-scope case above of code already running as you. A server started from a source checkout (`npm run dev`) has no secret; there the Host, Origin and fetch-metadata checks are the whole gate, and a local process that forges those headers is not refused.
- **Only the app window holds the server's secret.** In a packaged build every API route except the version, the handshake and the engine bridge needs the per-launch secret, and only the app's own window carries it. The in-app browser runs in its own session, which never receives the secret; it can still reach other ports on 127.0.0.1, such as a dev server you run. Routes that take a path from the window confine it: the git workbench reads, stages and discards only the files and branches a request names inside the project, and opening a project in an editor or Finder hands the opener only an absolute folder that exists.
- **The desktop shell.** The app window is sandboxed and context-isolated, with no Node integration. Every IPC handler checks that the sender is the app's own main frame, and a link leaves the app only as http, https or mailto, through your system's default handler. The `runAsNode` fuse is on, because the engine bridge runs as a Node child of the app, so a program running as your user can run JavaScript as the signed Cloaky binary and inherit what macOS granted it, such as the microphone; that is the out-of-scope case of code already running as you.
- **Undo under container isolation.** Undo restores files on the host, outside the container. It re-checks each file's folder right before every rename and delete that lands a restored file, so an agent in the container that swaps a folder for a symlink while you click Undo can redirect only an operation that lands in that instant. Node offers no `renameat`, so the window can be narrowed but not closed.
- **Credentials.** In the desktop app and its Mac login item the Venice key is encrypted with Electron's safeStorage, whose key the macOS Keychain or the Linux secret service holds, and `~/.cloaky/config.json` keeps only the ciphertext. A server started from a source checkout (`npm run dev`, `npm start`), Linux without a secret service, and Linux with the login service turned on keep the key in plaintext in `config.json` with 0600 permissions, so only your user can read the file. Transcripts, tool output and logs under `~/.cloaky` can hold sensitive material too.
- **Previews** of HTML and SVG an agent writes load in a sandboxed frame on a separate origin, under a policy that blocks fetch, XHR, WebSocket, beacons, remote scripts and images, and form posts. `npm run check:sandbox` checks each of those against a real browser. WebRTC is not blocked: Chromium does not enforce the policy directive that would stop it, so a preview can still reach the network that way.
- **Chromium's sandbox in the Linux AppImage.** The AppImage is built with electron-builder's default launcher, which starts Cloaky with `--no-sandbox` when the system does not allow unprivileged user namespaces; some distributions, Ubuntu 24.04 among them, restrict them by default. The desktop entry inside the AppImage, which AppImage integration tools copy to your applications menu, starts it with `--no-sandbox` every time. With that flag Chromium's renderer sandbox is off, so a page that exploits a Chromium bug, in the in-app browser or in a preview, is not contained by the operating system. The `.deb` and `.rpm` keep the sandbox.
- **Updates.** The app asks the update feed nothing until you press Check for updates or turn on automatic checks. It reads the feed from the public `cloakydev/cloaky` releases over HTTPS and never offers a version older than the one running. The feed and the packages come from the same release, so the feed's hashes alone would trust whoever can change that release; each platform therefore checks something the release cannot forge. macOS installs a downloaded update only when it is signed with Cloaky's Developer ID (Squirrel.Mac checks it against the running app's designated requirement). On Linux every feed entry carries an Ed25519 signature made with Cloaky's release key, which is kept outside the release repository, and the app installs a download only when its hash matches an entry signed for the offered version. Without that key, a changed GitHub release cannot get a Linux install to take its package. The `.deb` and `.rpm` install an update as root after your system's authentication prompt; the AppImage replaces its own file.
- **Release integrity.** macOS release builds are signed with our Developer ID certificate and notarized by Apple; `spctl -a -vv /Applications/Cloaky.app` reports `source=Notarized Developer ID`. Every release carries a `SHA256SUMS` file over all packages; it shows a download arrived intact, but it comes from the same release, so it does not prove where the package came from. The `.deb` and `.rpm` carry no package signature, and the update check above covers updates made in the app, not a package you download yourself. Builds are made from a clean checkout of the tagged commit, with the same signing, notarization and checks for every package.
