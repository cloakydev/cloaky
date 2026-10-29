# Changelog

Notable changes to Cloaky, newest first. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and versions follow [Semantic Versioning](https://semver.org/spec/v2.0.0.html). Each pull request's `Release notes:` line is the source for these entries.

## [Unreleased]

## [0.1.3] - 2026-10-29

Talk with the agent and hear it answer. Claude Code is now optional, and no engine holds your Venice key.

### New

- Voice
  - Turn on **Conversation** in the composer to hear each reply as it streams. A finished take sends itself.
  - Say "remember that …" in a conversation to save a memory instead of sending.
  - Talk-back says a short line when a run finishes or needs you. It uses Venice's Private voice, at about $0.0001 a line.
  - Talk-back stops for the day at 150,000 characters, about $0.53. Change the limit in **Settings → Voice**.
  - **Read this** reads a reply aloud.
  - Approve, answer a question or stop a turn by voice. An approval waits for a second "yes".
  - Hold the dictate chord to talk, or tap it to start and stop.
  - Dictation starts faster: whisper stays loaded between takes.
  - The voice bar tells you when the microphone is slow to open, hears nothing, or nears the 12-minute limit.
  - Voice typing works on Linux.
- Every engine comes with the eight Know Your Code skills, which Conversation is built on.
- Start a goal from a project.
- Turn Claude Code off in **Settings → Engines**. Threads, checks and scheduled tasks then run without it, and Cloaky fetches nothing for it.
- Save memories and stop a single background command in OpenCode, Codex, Kimi Code and Grok Build chats.
- The agent asks before editing when a request leaves the result open.

### Changed

- OpenCode, Codex, Kimi Code and Grok Build reach Venice through Cloaky, so none of them holds your key.
- Their spend is what Venice charged. Codex's is estimated from Venice's prices.
- They stop at the turn limit and at your session spend limit, and Stop always wins.
- They edit files with their file tools, so every change shows in Review and can be undone.
- In an encrypted chat, **Read this**, **Refine** and side chats are off, so nothing goes to a second Venice call.
- **Refine** works with any default model and keeps code blocks whole. It answers in the language you wrote in.
- Every palette meets its contrast floor. In dark mode, Send and the main buttons are a deeper blue with white text.
- Every control shows a hover and a press, and streamed words fade in.
- A reopened thread keeps its mode, effort and cost.
- A scheduled task starts at its next scheduled time and never waits on a question nobody is there to answer.
- Errors from git, Venice and teammates now say what happened in plain words.
- Cloaky checks for updates as soon as you turn automatic checks on.

### Fixed

- Undo
  - Starting a turn no longer freezes Cloaky while Undo saves the project.
  - Undo works on large projects, survives the app quitting mid-save, and keeps its history when its index is damaged.
  - Undo says why it was held when the project folder moved.
- Privacy
  - Claude Code no longer contacts api.anthropic.com unless you have added a plugin marketplace.
  - Incognito chats on the other engines write nothing to disk.
  - The privacy page lists what dictation and talk-back send and keep.
- Review and git
  - Commit includes only what Review showed.
  - Cloaky refuses to commit during a merge or rebase, and pushes to the repository's own remote.
- Venice
  - Tokens, reasoning and each turn's cost are counted correctly.
  - Tool calls stay whole when a reply is cut off.
  - Changing the model no longer resets an engine session's spend count.
- Voice
  - Dictation no longer stops mid-take or hangs for 20 seconds when whisper fails to start.
  - A take keeps its words after its composer closes.
  - Conversation no longer reads inline code aloud.
- Threads
  - A tool call you deny shows as denied, not failed.
  - A question's row shows the question instead of its raw input.
  - The note above a reply names the model the way the picker does.
  - A stopped message says in plain words how its run ended.
- Squad
  - Each teammate gets its own workspace, and a message reaches the teammate it mentions.
  - A room keeps working when its lead is deleted.
- A damaged settings, Undo or history file is set aside and named on the start page.
- Archiving, renaming or deleting a thread applies in every open window.
- Long names and unbroken strings wrap or truncate instead of breaking the layout.
- An engine no longer triggers a macOS folder-access prompt for the folder Cloaky was launched from.
- Dependencies are updated past their security advisories.

### Known issues

- The Intel and Linux builds have had less testing than Apple silicon.
- Voice has not been tried on a Linux desktop yet.

## [0.1.2] - 2026-10-20

Cloaky now runs on Linux.

### New

- Linux builds for x64: AppImage, `.deb` and `.rpm`.
- Model prices and context size in the model picker.
- Tokens and cost on each turn.
- Your Venice balance in the sidebar.

### Changed

- Claude Code is no longer required. Any installed engine works.
- New installs limit each session to $5 of spend. Change it in Settings › General.
- A refreshed look.

### Fixed

- Send waits for uploads and voice input to finish.
- Smaller layout and keyboard fixes.

### Known issues

- The Intel and Linux builds have had less testing than Apple Silicon.

## [0.1.1] - 2026-10-13

The first public release, a beta: it is ready for daily work, and some edges are still rough. Report a problem or ask a question in [Discussions](https://github.com/cloakydev/cloaky/discussions); report a security issue privately, as [SECURITY.md](https://github.com/cloakydev/cloaky/blob/main/SECURITY.md) describes.

### Added

- A desktop app for macOS 13 or later, for Apple Silicon and Intel Macs, that runs Claude Code, OpenCode, Codex, Kimi Code and Grok Build on Venice models with your own Venice API key.
- An onboarding step that shows which engines are installed on this machine and, for the rest, each vendor's own install command. Cloaky runs the engines you install and keeps none of its own; each engine updates itself. To drive your `claude`, Cloaky fetches the newest Claude Agent SDK from registry.npmjs.org, checked against its published SHA-512, at start and once a day.
- Approval cards for edits and commands, and permission modes that set what runs without asking.
- A review pane for the working tree: diffs per file, comments on a line, and rejecting a hunk.
- Undo on every agent turn, on the turn's receipt in the transcript, for Claude Code and the four ACP engines alike. A turn that ran alone in a project is bounded by two snapshots of the project in a shadow Git repository under the state root, so Undo also restores what a shell command wrote; a plain chat, a teammate whose files live on its own computer and a broad folder such as your home keep a per-file checkpoint of what the engine's edit tools touched. Undo writes back only a file that still holds what the turn left, keeps one changed since, and names on its hint the files a shell command or you changed during the turn. A receipt with no Undo says why. A session keeps Undo for its last ten turns.
- Redo after an Undo. Before it writes anything, Undo keeps the bytes and metadata it is about to replace, so a file a shell command moved in from outside the project is never lost; the receipt then offers Redo, and after a Redo, Undo again. An Undo or Redo that puts nothing back keeps its checkpoint and says why on the card, and one whose run closed before it answered is offered again with its outcome marked unconfirmed.
- A Board for Imagine. A thread that has generated an image or a clip gets a Board toggle on its top bar, and each project gets a Board row in the sidebar that gathers the generations of all its conversations, newest first. Filter by kind and model, open a generation whole, copy its prompt or save it, show it in Finder in the desktop app, or save up to 64 at once to one folder. A thread's Board loads a generation's prompt and settings back into the composer; a project's Board reuses a prompt or starts a new generation in its own conversation.
- A page check for coding runs, Solo and Squad: the agent can open a page on an approved local dev server in a hidden window of the desktop app and read its status, errors, failed requests, text and a screenshot. A new address asks first, with the same consent as the Preview pane; the row shows the errors and a thumbnail.
- An update channel in Settings › General: Stable, the default, or Beta. Switching back to Stable drops a beta that was downloaded but not installed, and never offers a build older than the one running.
- Six community palettes, Catppuccin, Tokyo Night, Rosé Pine, Nord, Dracula and GitHub, beside Cloaky grey (the default), Venice and Contrast, each held to Cloaky's contrast floors in both themes.
- A Motion setting in Settings › Appearance for the lit call to action, the one filled ask on each screen (Send or Steer in the composer, Continue or Answer on a question card, the next setup step): Orbit, the default, runs a thin arc of light round its edge; Sweep and Rise light it under the pointer; Still keeps it lit and unmoving. Reduced motion stops or fades the light, and a disabled button is never lit. While a question card is open, it holds the lit ask and Steer is drawn plain.
- A Settings row that signs the in-app browser out of every site, apart from Start fresh, which keeps those sign-ins.
- Cloakster, an optional second opinion on a session: after each turn it asks Venice's decision model whether the agent is looping, has drifted, claims a result no tool output shows, or looks finished, and when an approval card opens, whether the call reaches outside the task, cannot be undone, or sends something out. It also judges each message before its turn (large enough to plan first, too vague to start on, touching data or deployment) and, if you ask, sets the turn's effort from the request's size; it writes the session headline as a typed pick instead of a generated line; it checks each turn's change against the rule lines in CLAUDE.md and AGENTS.md; it notices an agent retrying an approach its own results contradicted; and, if you ask, it runs the project's checks when a turn claims to be done. Off until you turn it on in Settings.
- The agent asks instead of guessing when a request is vague. As a Solo agent turn starts, Cloakster asks whether the request names what to change and where: Venice's decision model when it is as private as your model, otherwise your own model, so the request never goes to a model Venice ranks less private than yours. Cloaky treats the decision model as `anonymized`, the tier Venice's model list gave it on 2026-09-19, so it reads only requests from threads on `anonymized` models. When the request does not say, the engine is told to put you two or three quick multiple-choice questions, drawn from what it sees in the project, before it edits anything; you pick, it continues. A line above the composer says what was unclear. The message is never held back, and a question of your own is never judged. On for new installs and independent of the rest of Cloakster; a saved Cloakster setting from an earlier version keeps what it did. Settings › Cloakster turns it off.
- A Cloakster card under the Environment card: whether the thread is being watched and why not if not, how many questions Jev answered and how many led to a card or a pause, the tokens, cost and typical latency, and an Inspect fold listing every check with its probabilities.
- Every agent session's system prompt now ends with a line asking the model to read with the Read tool and edit with the Edit and Write tools rather than through shell commands, and to stay inside the project folder. On ten user-shaped tasks with DeepSeek V4.1 Flash, this cut shell commands by 42%, model turns by 24% and output tokens by 22%, with the same 74 of 74 checks passing, about the same cost and 25% more wall time; edits made from the shell were bypassing the review pane and Undo.
- On the Claude engine, in every permission mode but full access, a shell command that writes a source file (a redirect or `tee` into one, `sed -i` or `perl -i`, or an inline Python or Node program that writes a file) is refused where it would otherwise ask for approval, and the agent is told to make the change with the Edit or Write tool instead.
- The Cloaky License: Cloaky is free to download and use, for personal and commercial work. It is not open source yet, so please do not redistribute, resell or rebrand it; we plan to open-source it later. Third-party components keep their own licenses, listed in NOTICES.md.
- The pull request row in the Environment view lists every check by name, says what blocks the merge, and merges with any method the repository allows, behind a confirm that names the head commit it merges. The merge is refused if new commits reached the branch after you looked. Deleting the branch deletes only the copy on GitHub; nothing local changes. The row reads again every 15 seconds while a check runs.
- A Venice request that fails before any of its answer arrives (a 500, 502, 503 or 504, an overloaded service, a rate limit that asks for a short wait, or a connection that failed at once) is sent again, up to three attempts in all, before the engine sees an error. A request to an end-to-end encrypted model is never sent twice.

### Changed

- A new interface, rebuilt screen by screen on React 19, Tailwind v4 and a Base UI kit: one design system across Solo and Squad, a visible focus mark on every control, official brand marks for providers and services, and the same routes, frames and behaviour as before.
- File-editing turns run the project's own detected checks by default (Verify on done), after one approval per run; a project with no checks runs none.
- The composer's model picker, permission mode, chips and overflow are drawn as borderless menu rows, the way the menus they open are.
- Replies sit on a 1.5 line height with 8px between blocks, and the transcript's rows, bubbles and decision cards are packed tighter.
- A thread of more than 24 turns draws only the turns around what you are reading, with the rest held at their height, so opening a long thread no longer lays out every turn it holds. The timeline and search in the thread reach every turn, and a step you opened stays open when you scroll back to it. Find in page and Select All see only the turns drawn at that moment.

- Claude Code runs no longer fetch Anthropic's MCP server list from api.anthropic.com at each session start, and an update check sends GitHub no per-install id.

### Removed

- `GET /api/git/prstatus`. `GET /api/prs/current` answers the same question with checks and merge state.

### Fixed

- A model Venice has switched off while it is still in the catalogue now reads as "Venice has taken this model offline for now. Pick another model, or try again later" instead of a note about the request's shape.
- A request to explain something, such as "tell me about this project", is no longer judged as too vague to start on, and the reply it earns is no longer asked to back up a change it never claimed. "Built on Next.js" opened a project overview and read as a claim of having built it; the agent was sent a follow-up in the person's own words and spent a turn denying it.
- Cloakster's spend on judging a message before its turn now reaches the session's ledger. It was charged before the session had an id and dropped, so the Usage page showed about half of what Venice billed for a one-turn session.
- Stop, or an engine that goes away, now cancels the Venice request it was waiting on or reading, so a reply nobody will read is no longer generated to its end and billed.
- An engine that closes its output and stays up is stopped after five seconds and the turn ends with a sentence saying so, instead of waiting out the 15-minute silence deadline.
- A Claude Code engine that exits cleanly in the middle of a turn now says the turn did not finish, instead of leaving the message unanswered.
- A Claude Code engine that is missing or cannot run names its path and the command that installs it, instead of a JavaScript error or advice about musl loaders.
- An OpenCode, Codex, Kimi Code or Grok Build engine that dies without naming an error shows the last three lines it printed, with credentials masked.
- A Venice stream that breaks after it began is reported in the run, instead of reaching it as an unnamed error.

- The sidebar keeps every row's focus mark whole when it scrolls a row into view, and Tab and Shift+Tab stop on the same rows.
- A long reply streams without redrawing the turn timeline or rewriting the saved settings on every frame.
- The update band says in one sentence why a check failed, instead of printing the request and GitHub's headers.
- A project opened in a new folder appears in the sidebar as soon as its run starts, in every window, and keeps its place in the list for the session.

### Known issues

- The Intel build has had less testing than the Apple Silicon build.
- This release is for macOS only. Linux packages come in 0.1.2; there is no Windows build.

[Unreleased]: https://github.com/cloakydev/cloaky/compare/v0.1.3...HEAD
[0.1.3]: https://github.com/cloakydev/cloaky/compare/v0.1.2...v0.1.3
[0.1.2]: https://github.com/cloakydev/cloaky/releases/tag/v0.1.2
[0.1.1]: https://github.com/cloakydev/cloaky/releases/tag/v0.1.1
