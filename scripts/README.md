# Scripts

Utility scripts for operating `codex-agent-org`.

## `run-claude-agent`

Runs Claude Code Agent with `claude -p` and saves prompt, command, stdout, stderr, exit metadata, and git diff under `runs/<timestamp>-<name>/`.

```bash
scripts/run-claude-agent \
  --prompt tasks/claude-code-smoke-test.md \
  --cwd . \
  --name smoke-test \
  --timeout-sec 120
```

Use `--dry-run` to inspect the planned run directory and command without executing Claude Code.

On macOS, install Homebrew coreutils if you want timeout enforcement:

```bash
brew install coreutils
```

The runner automatically uses `gtimeout` when it is available.
