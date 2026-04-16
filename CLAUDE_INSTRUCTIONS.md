# Instructions for Claude

This repo is the source of truth for FCR/Jane handoff documentation. Read this when Travis says "pull Jane handoff" or similar.

## New session startup procedure

1. Fetch this file first: `https://raw.githubusercontent.com/travisgehring2026/fcr-jane-handoff/main/00_INDEX.md`
2. Fetch `01_CORE_HANDOFF.md` — current system state, rules, infrastructure
3. Fetch `02_ROADMAP.md` — priorities and phase order
4. Fetch whatever's relevant to the current task (e.g., `03_PHASE0_SPEC.md` if building the lifecycle)
5. Check `sessions/` for the most recent session log if context on last session is needed

## Raw GitHub URL pattern

All files are fetchable at:
```
https://raw.githubusercontent.com/travisgehring2026/fcr-jane-handoff/main/{filename}
```

For files in subdirectories:
```
https://raw.githubusercontent.com/travisgehring2026/fcr-jane-handoff/main/sessions/{filename}
```

**Note:** This is a PRIVATE repo. The URL will only work if the GitHub token is configured. If web_fetch fails with 404, the token may have expired. Ask Travis to regenerate at https://github.com/settings/tokens/new with `repo` scope.

## End-of-session procedure

1. Update relevant markdown files with what was built/changed
2. On VPS:
```bash
cd /root/jane-workers/handoff-repo
set -a; source /root/fcr.env; set +a
git add -A
git commit -m "[session] YYYY-MM-DD: brief summary of what was done"
git push origin main
```

## Rules

- **Never store API keys, tokens, or passwords in this repo.** Credentials live in `/root/fcr.env` only.
- **Update 00_INDEX.md** if you add new files to the repo.
- **One session log per session** in `sessions/YYYY-MM-DD_topic.md`.
- **Keep docs concise.** These are handoff docs, not novels. Focus on: what exists, where it is, what state it's in, what to do next.
- **Commit at session end.** Don't leave uncommitted changes.

## VPS access

Travis runs all commands on the VPS via SSH. Claude in this chat cannot SSH directly — provide commands for Travis to paste.

SSH: `ssh root@138.68.26.63`
Env: `set -a; source /root/fcr.env; set +a`
Handoff repo on VPS: `/root/jane-workers/handoff-repo/`
