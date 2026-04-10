# rules-gemini-cli

Gemini CLI governance rules — guards against README prompt injection (discovered 2 days after launch), eval injection that bypasses the allowlist, GDPR non-compliant data collection, and hidden command execution via markdown files.

**12 rules · 3 files**

![rules-gemini-cli — AI agent governance demo](demo.cast)

> [▶ Watch interactive demo on SigmaShake Hub](https://hub.sigmashake.com/ruleset/rules-gemini-cli)

## Install

```bash
ssg hub pull rules-gemini-cli
```

Available on the [SigmaShake Hub](https://hub.sigmashake.com) — the open registry for AI agent governance rules. Compatible with Claude Code, Cursor, Windsurf, and any AI coding agent using the `ssg` hook protocol.

## Rules

### geminicli_exec_injection.rules — Execution injection safety (5 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-gemini-readme-pipe-injection` | DENY | error | Blocks piping/chaining README content with shell commands (day-2 vulnerability) |
| `no-gemini-eval-injection` | DENY | error | Blocks eval with variable expansion (documented allowlist bypass) |
| `no-gemini-allowlist-bypass` | DENY | error | Blocks sandbox/allowlist override via env vars or CLI flags |
| `ask-gemini-default-invocation` | ASK | warning | Prompts on default Gemini CLI invocations due to GDPR data collection |
| `log-gemini-auto-execute-mode` | LOG | info | Audits auto-execute/non-interactive mode (highest injection risk) |

### geminicli_read_prompt_injection.rules — Prompt injection via files (3 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `ask-gemini-readme-with-commands` | ASK | warning | Prompts when README contains bash/sh code blocks before Gemini reads it |
| `no-gemini-hidden-html-commands` | DENY | error | Blocks hidden shell commands in HTML comments within markdown |
| `log-gemini-markdown-documentation-read` | LOG | info | Audit trail for all .md/.rst file reads by Gemini CLI |

### geminicli_write_privacy.rules — Privacy and config safety (4 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-gemini-telemetry-enable` | DENY | error | Blocks enabling additional telemetry (GDPR non-compliance documented) |
| `ask-gemini-gcloud-credential-access` | ASK | warning | Prompts before GCloud application default credential access |
| `no-gemini-sandbox-disable` | DENY | error | Blocks disabling the sandbox/allowlist mechanism |
| `log-gemini-project-config-init` | LOG | info | Audit trail for .gemini/ and .geminirc file creation |

## Why this matters

Gemini CLI launched on June 25, 2025. Security researchers found its first critical vulnerability **within 48 hours**:

- **README prompt injection** (Tracebit, June 27, 2025, patched v0.1.14): Gemini CLI reads README.md files to understand code context. Attackers hide natural language commands in README files that Gemini interprets as instructions and executes silently. The attack vector: open-source packages with poisoned READMEs.
- **Eval injection bypass** (GitHub issue #5495): `eval "$variable"` in shell scripts bypasses Gemini CLI's allowlist restriction mechanism, executing commands that would otherwise be blocked.
- **Allowlist bypass** (GitHub issue #8953 and Tracebit): The allowlist intended to restrict command execution was improperly implemented, enabling bypass.
- **GDPR non-compliance** (GitHub issue #1489): No privacy notice, no data collection disclosure, and no opt-out mechanism in the CLI. Your prompts and code are collected by default.

## Compatible AI clients

- Gemini CLI (primary target)
- Works alongside: `rules-security`, `rules-privacy`

## About

Part of the [SigmaShake Hub](https://hub.sigmashake.com) — open-source governance rules for AI coding agents.
Install the `ssg` CLI to enforce these rules: `npm install -g @sigmashake/ssg`
