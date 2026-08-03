# Agent Notes

- Keep changes tight and minimal.
- Do not assume backwards compatibility; ask when unsure.
- Use concise conventional commits, scoped to the touched surface:
  - `fix(TUI) - fixed typo in xyz`
  - `fix(tray) - aligned xyz`
- Before finalizing, confirm the requested task and any follow-up work are complete.

## Cursor Cloud specific instructions

- Single Rust crate (edition 2024, needs rustc >= 1.85). Stable toolchain, `cargo`, `clippy`, and `rustfmt` are preinstalled; the startup update script runs `cargo fetch`.
- Build/lint/test/run commands are standard cargo and already documented in `README.md` (`## Build`, `## Validation`) and mirrored in `.github/workflows/ci.yml`. Run the app in dev mode with `cargo run -- <subcommand>` (e.g. `cargo run -- status`); no args launches the interactive TUI.
- Only the CLI/TUI build on Linux; the system tray (`src/tray.rs`) and its deps are `cfg(target_os = "windows")`-gated, so tray behavior cannot be exercised here.
- The app reads the live Codex login from `~/.codex/auth.json` + `~/.codex/cap_sid`. To exercise `save`/`activate`/`status` without a real Codex account, write a fake `auth.json` whose `tokens.id_token` is a JWT (`header.payload.` with a base64url-encoded payload carrying `email`/`sub`/`name`/`https://api.openai.com/auth.chatgpt_plan_type`); see `src/identity.rs`.
- Saved snapshots live at `~/.local/share/codex-account-switcher/` on Linux (the `directories` crate uses only the app name, NOT the `nextide/...` path implied by the README). Delete that dir to reset saved-account state between test runs.
- `usage`, and the usage enrichment in `list`, call OpenAI/ChatGPT network endpoints. Offline they degrade gracefully to `usage unavailable`; this is expected, not a failure.
