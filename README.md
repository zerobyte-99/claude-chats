# claude-chats

Small single-file Python tool (stdlib only) to **list, rename, delete and move**
saved [Claude Code](https://claude.com/claude-code) and [Codex](https://github.com/openai/codex) chats.

```bash
claude-chats list                    # Claude Code chats for the current directory
claude-chats list -a -n 20           # 20 newest across all directories
claude-chats list -p myapp           # working directory containing "myapp"
claude-chats rename 4af8c6ed "Ollama tuning"
claude-chats move 111bafee 4af8c6ed ~/other/project   # last arg is the destination
claude-chats delete 16b74f11 -y

codex-chats list -a                  # same commands, for Codex chats
```

`codex-chats` is a symlink to the same script; the command name picks the tool.
IDs are unambiguous prefixes of the session id (shown by `list`).

## Install

```bash
git clone https://github.com/zerobyte-99/claude-chats ~/claude-chats-repo
ln -s ~/claude-chats-repo/claude-chats ~/bin/claude-chats
ln -s ~/claude-chats-repo/codex-chats  ~/bin/codex-chats   # ~/bin must be on PATH
```

## How it works

| | Claude Code | Codex |
|---|---|---|
| Data | `~/.claude/projects/<cwd>/<id>.jsonl` | `~/.codex/sessions/Y/M/D/rollout-*-<id>.jsonl`, `state_5.sqlite`, `session_index.jsonl` |
| rename | appends `custom-title` / `agent-name` records | sets `threads.name`, appends to `session_index.jsonl` |
| move | moves the file (and subagent folder) to the new project folder, rewrites `cwd` | rewrites `cwd` in the rollout and `threads.cwd` |
| delete | removes the file and its folder | removes the rollout, DB rows and index entries |

Codex guardian/subagent threads are hidden from `list`.
`CLAUDE_CONFIG_DIR` and `CODEX_HOME` are honored.

## Caveats

- Delete is permanent (confirmation prompt unless `-y`).
- Don't modify a chat that is open in a running session.
- Codex's `state_5.sqlite` is an internal format and may change between versions; back up `~/.codex` first if unsure.
