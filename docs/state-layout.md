# State layout

Cloaky keeps its state in one directory, the state root: `~/.cloaky`, or the absolute path in `CLOAKY_STATE_ROOT`. This page lists every entry the code writes there.

**Start fresh** is Settings › Start fresh. "Yes" means it removes the entry. "Everything" means it removes the entry only when "Also delete workflows and scheduled tasks" is on. The server refuses Start fresh while any run is live.

**Delete by hand** assumes Cloaky is quit and its login server, if turned on, is stopped. "Safe" means Cloaky writes the entry again or downloads it again when needed. Anything else names what is lost. Deleting the whole state root resets Cloaky to a first launch, including the Venice key.

Several stores also take an environment variable that points them elsewhere (`CLOAKY_CONFIG`, `CLOAKY_LEDGER_FILE`, `CLOAKY_MEMORY_DIR` and others).

## Settings and identity

| Entry | Holds | Start fresh | Delete by hand |
| --- | --- | --- | --- |
| `config.json` | App settings and the Venice key (sealed by the desktop shell, or plain text at mode 0600) | No | Loses every setting and the key |
| `local-auth.json` | The running server's port and local secret, at mode 0600, so a desktop shell can attach to a server it did not start | No | Safe; the server writes it again at start |
| `opened-projects.json` | Project folders opened through the picker, which the project guard then admits | No | Folders outside the home directory must be picked again |
| `script-trust.json` | Approvals for scripts that arrive inside a repository, such as `.cloaky/worktree-setup.sh` | Everything | Each script asks for approval again |
| `desktop-window.json` | The window's size and position | No | Safe |
| `desktop-profile/` | Chromium's profile, only when `CLOAKY_STATE_ROOT` is set; otherwise the shell uses Electron's default profile outside the state root | No | Loses the interface's browser storage for that root |
| `desktop-profile/Partitions/cloaky-browser/` | The in-app browser's session (`persist:cloaky-browser`): cookies, site storage and cache of the pages opened in it. Without `CLOAKY_STATE_ROOT` it is in Electron's default profile: `~/Library/Application Support/Cloaky/Partitions/cloaky-browser` on macOS, `~/.config/Cloaky/Partitions/cloaky-browser` on Linux | No; Settings › Venice & privacy › In-app browser sign-ins clears it | Signs out of every site opened in the in-app browser |

## Conversations

| Entry | Holds | Start fresh | Delete by hand |
| --- | --- | --- | --- |
| `engine-threads/claude/` | The Claude engine's config home (`CLAUDE_CONFIG_DIR`): transcripts under `projects/`, side chats beside them, and the user-scope settings, CLAUDE.md, agents, skills, commands, output styles and MCP servers set in Settings | Partly: the transcripts of sessions Cloaky started, and every worktree project folder | Loses every Claude thread and the engine settings made in Cloaky |
| `engine-threads/claude/.cloaky-kycode.json` | What Cloaky did with each of Know Your Code's eight skills in the config home's `skills/`: the hash of a skill it wrote, or that the skill was edited, deleted, or there before the first install, so a skill is written again only while it is unedited | No | Each skill is checked again at the next start: one byte-equal to this version's is recorded as Cloaky's, any other is left alone as the person's, and one that is gone is not written back |
| `engine-skills/kycode/` | A read-only copy (files at mode 0444) of Know Your Code's eight skills. The index OpenCode, Kimi Code and Grok Build are given names each skill's SKILL.md here. Written at each start, and replaced whole when it differs from this version's | No: sessions started before the next start still read it | Safe; the next start writes it again, and until then ACP sessions are sent no index |
| `engine-threads/opencode/` | OpenCode's session database (`opencode.db`) and data directory (`data/`) | Yes | Loses every OpenCode thread |
| `engine-threads/<engine>/` (codex, kimi, grok) | One engine home per run whose thread can be reopened, and a `<session id>.home` pointer to it | Yes | Loses those engines' threads |
| `engine-config/<run>/` | OpenCode's per-run provider config; removed when the run closes | No | Safe |
| `transcripts/` | The transcript mirror: one JSONL file per session of what Cloaky streamed; the only transcript an ACP thread has besides its engine's own | Yes | Loses Cloaky's record of each session, and the ACP threads' history in the sidebar |
| `acp-threads.json` | The ACP thread index: each session's project, engine and title | Yes | ACP threads leave the sidebar |
| `project-media-threads.json` | Project-native media threads' exact project directories, titles and times; generated turns stay in the transcript mirror, and the first coding turn adopts the same session id | Yes | Media-only threads leave the sidebar and Board; native engine transcripts remain |
| `chats/index.json` | The plain-chat index: each chat's home, session, title, model and times | Yes | Plain chats leave the sidebar |
| `Chats/` | Plain chats' working folders, only when `CLOAKY_STATE_ROOT` is set; otherwise they are in `~/Cloaky/Chats` | No | The folders hold no conversation data |
| `thread-settings.json` | Each thread's model, permission mode and effort, where it overrides the default | No | Threads fall back to the defaults |
| `goals.json` | Each thread's goal and its timer | Yes | Loses the goals |
| `goal-plans.json` | Each goal plan in a project: its ask, budget, state and threads | Yes | Loses the goal plans; their threads stay |
| `snoozes.json` | Snoozed threads | No | Snoozed threads come back |
| `runs.json` | The run journal: every live run, so a crash can offer to resume it | Yes | Loses the resume offer after a crash |
| `interrupted.json` | An older crash marker; deleted at server start | Yes | Safe |
| `input-facts/` | Input custody: each message sent to a run and whether it was delivered | Yes: payloads purged, each record kept as a deleted marker | Loses the record of which messages were delivered |
| `permission-facts/` | Permission custody: each permission card and its answer | Yes: payloads purged, each record kept as a deleted marker | Loses the record of answered cards |
| `attachments/` | Files attached to messages, one directory per upload | No | Loses attachments that threads still mention |
| `media/` | Generated images and videos, by session | Yes | Loses the media in threads |
| `media-pending.json` | Videos Venice is still rendering, so a restart can collect them | Yes | Those videos are not collected |
| `voice-speech/` | Talk-back: `usage.json`, the local day and the characters sent to Venice that day, read against the daily cap, and `cache/`, one mp3 per fixed attention line and voice Venice has spoken, named by a hash of the model, voice and line (at most 300, oldest dropped first; a `.part` file is a write cut short). The folders at mode 0700, the files at 0600. A reply read aloud is never kept | Yes | Today's count starts again at zero; kept lines are spoken (and paid for) again |
| `memory/` | Memory records, one JSON file each | No | Loses every memory |

## Records

| Entry | Holds | Start fresh | Delete by hand |
| --- | --- | --- | --- |
| `ledger.json` | The token and cost ledger, per session | Yes | Loses usage and cost history |
| `usage-cache.json` | Usage figures computed from transcripts, cached per file | Yes | Safe |
| `venice-catalogue.json` | The last Venice model catalogue read in full, answered (marked stale) while Venice cannot be reached | Yes | Safe; the next successful read writes it again |
| `file-stats.json` | Each session's per-file edit weights, measured when the edit landed | Yes | Loses the +/− figures; they cannot be measured again |
| `checkpoints/` | Per-turn file snapshots behind Undo | Yes | Loses Undo on past turns |
| `snapshots/<project digest>/` | A shadow Git repository per project (`shadow.git`) holding the trees that bound each turn, with copies of the project's files, and `index.json` naming the trees the kept checkpoints use. Excluded: the project's ignored paths, `.git`, `node_modules`, `.venv`, `venv`, `target`, `dist`, `build`, `.next`, `coverage`, `*.log`, `.env` files and files over 2 MiB. Deleting a thread drops its trees and the shadow once no thread names it; the checkpoints' byte budget counts it | Yes | Loses the whole-project Undo on past turns |
| `audit/` | `<session>.jsonl` (every shell command a session ran), `hands-<teammate>.jsonl`, `unattended.log` (what teammates and routines did unattended) and `cloakster.log` | Yes | Loses those records |
| `findings.json` | Security scan findings and their triage | Yes | Loses findings and false-positive marks |
| `verification/<project digest>/` | Receipts and logs of project checks, and a lock | No | Safe; checks run again |
| `logs/` | The server's and the desktop shell's console, one file a day | No | Safe |
| `launchd.out.log`, `launchd.err.log` | The macOS login server's output; written to `~/.cloaky` even when `CLOAKY_STATE_ROOT` is set | Everything | Safe |

## Workflows, schedules and Squad

| Entry | Holds | Start fresh | Delete by hand |
| --- | --- | --- | --- |
| `workflows.json` | Saved workflows (prompt chains) | Everything | Loses the workflows |
| `schedules.json` | Scheduled tasks and routines | Everything | Loses every schedule and routine |
| `squad.json` | The roster: teammates (with each one's always-allowed hands sites), rooms, their thread bindings and retained session locations | Partly: the session locations of transcripts it removed; teammates and rooms stay | Loses the whole Squad |
| `squad/<teammate>/` | A teammate's own state, such as its memory file | No | Loses that teammate's memory |
| `squad-queue.json` | Messages accepted for teammates and not yet settled | No | Loses undelivered messages |

## Engines, sandbox and models

| Entry | Holds | Start fresh | Delete by hand |
| --- | --- | --- | --- |
| `engines/claude/<version>/` | The Claude Agent SDK Cloaky fetched from registry.npmjs.org to drive your `claude`; only the newest is kept, and engine folders an earlier build installed are removed at start | No | Safe; fetched again at the next start |
| `engines/persona/<run>/` | A run's persona file for an ACP engine | No | Safe |
| `engines/bridge/<run>/` | A run's bridge bundle; removed at close | No | Safe |
| `sandbox/shared/claude-home/` | The engine config home of a sandboxed session | No | Loses sandboxed sessions' transcripts |
| `sandbox/<teammate>/` | A teammate computer's engine home (`claude-home/`), screen secret and hands token | No | Loses that teammate's sandboxed transcripts; the secrets are minted again |
| `hands-host-ports.json` | Teammate computers that may still hold a task's host port open, so the next boot closes it (one Docker cannot yet answer for is kept and tried again) | No | A port left open by a crash stays open until that computer is next handed out |
| `sandbox/env/` | One environment file per sandboxed run | No | Safe |
| `sandbox/build-<version>/`, `sandbox/desktop-build-<version>/` | Staging for the sandbox and teammate images | No | Safe |
| `models/` | whisper.cpp speech models downloaded in Settings › Voice, each kept only once it matches its published SHA-256; `<model>.part` while one downloads, removed when the download fails or is cancelled | No | Safe; download again in Settings › Voice |
| `whisper-server.json` | The pid of the resident whisper-server while it runs and the path of the empty folder it serves (`cloaky-whisper-public-*` in the system temp folder), at mode 0600, so the next start ends one a crash left behind, only while that pid is still whisper-server serving that folder, and removes the folder. Removed when it stops and at that start | No | Safe; a whisper-server left behind is then not ended until reboot, and its empty folder stays until the system clears its temp folder |

[Data and privacy](PRIVACY.md) lists what Cloaky writes outside the state root.
