# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

`elf-express/org-skills` is a **[skillshare](https://github.com/runkids/skillshare)-managed distribution repo**, not an application. It aggregates AI "skills" (`SKILL.md` files) from upstream repos plus the team's own, and syncs them into one directory per AI CLI (`.adal/`, `.claude/`, `.codex/`, `.agents/`).

There is **no package.json, no build, no test suite, no linter.** Do not look for one. The `skillshare` CLI is the build tool; git is the delivery mechanism.

## Golden rule

[.skillshare/config.yaml](.skillshare/config.yaml) is the single source of truth. To change which skills exist or where they go: **edit `config.yaml`, then run `skillshare sync`.**

**Never hand-edit files under `.adal/`, `.claude/`, `.codex/`, or `.agents/`** — those are generated symlinks into the cache (see Architecture). Edits there either silently mutate the cached source through the link or get clobbered on the next sync. Real edits belong in:
- [.skillshare/skills/](.skillshare/skills/) `<group>/` — for the team's own (local) skills
- the upstream repo — for vendored skills

## Common commands

Run from the repo root. All accept `--dry-run`/`-n` to preview and `--help`/`-h`.

```powershell
skillshare sync                 # regenerate all targets from config.yaml (the "build")
skillshare sync --dry-run       # preview what sync would change
skillshare status               # show what's out of sync
skillshare diff                 # source-vs-target differences
skillshare list                 # list all skills
skillshare audit                # security scan (config blocks sync at CRITICAL)
skillshare doctor               # diagnose broken skills / symlinks
skillshare update --all         # pull latest for tracked sources, then sync
skillshare new <name>           # scaffold a new local skill
```

There is no separate "validate" step — the closest equivalents are `skillshare sync --dry-run`, `skillshare audit`, and `skillshare doctor`. Full command reference is mirrored in [docs/wiki/54 Commands.md](docs/wiki/54%20Commands.md).

Git workflow: branch is **`main`**, remote is `elf-express/org-skills`. `skillshare commit`/`push`/`pull` are thin git wrappers that also run sync; plain `git` works too.

## Architecture

**Config → cache → targets**, in three layers:

1. **Config** — [.skillshare/config.yaml](.skillshare/config.yaml) declares `targets` (which CLIs to sync to) and `skills` (sources, by GitHub path or local). [.skillshare/skills/.metadata.json](.skillshare/skills/.metadata.json) records the resolved version/hash of each fetched source.
2. **Source cache** — [.skillshare/skills/](.skillshare/skills/)`<group>/…/SKILL.md` holds the actual skill content: cloned upstreams (e.g. `affaan-m/`, `agent-browser/`) and the team's local skills (`api/` = `acme-api`, `deploy/` = `acme-deploy`, `ui/`).
3. **Targets** — each target dir (`.adal/skills/`, `.claude/skills/`, `.codex/skills/`, `.agents/skills/`) is a flat set of **symlinks** into the cache. Names are flattened with `__` separators and a `<group>___` prefix that preserves origin, e.g. `affaan-m__ECC___affaan-m__.agents__skills__api-design`. The `managed` map in each target's `.skillshare-manifest.json` records every symlink skillshare owns.

Target mapping (from `config.yaml`): `adal`→`.adal/`, `claude` (merge mode)→`.claude/`, `xcode-codex`→`.codex/`, `universal`→`.agents/`.

Tracked sources (`tracked: true` + `branch`, e.g. `_superpowers`, `_affaan-m`) are full repos kept on a branch and updated via `skillshare update`; untracked ones are pinned single-skill snapshots.

> The skill list Claude Code shows you aggregates project + user-level (`~/.claude/skills/`) + plugin skills. Not every available skill is defined in this repo — this repo's skills are those reachable from `config.yaml` / the cache.

## docs/

- [docs/wiki/](docs/wiki/) — `01`–`58`, **machine-translated read-only snapshots** of skillshare's official docs. Prose is auto-translated (Chinese, often rough); fenced code blocks stay English and are the reliable part. **Do not edit the translated prose** — they're verbatim snapshots (slated to be compiled into an MCP). Use them as a skillshare reference.
- [docs/superpowers/specs/](docs/superpowers/specs/) — design records for changes to *this* repo, dated `YYYY-MM-DD-<topic>.md`. (Singular `superpowers/` here is the repo's own design log, distinct from the vendored `superpowers` skill set.)

## Conventions & gotchas

- **LF everywhere.** [.gitattributes](.gitattributes) forces `eol=lf` on all text files. On Windows, don't reintroduce CRLF.
- **Selective tracking of generated output.** [.gitignore](.gitignore) ignores `_*/` group dirs under `.adal/skills/` and `.skillshare/skills/`, but group-*named* outputs (`superpowers___*`, `affaan-m__*`) **are** committed. So target dirs are partly tracked, partly regenerated — check `.gitignore` before assuming a synced file is or isn't in git.
- **Discovery filtering.** [.skillignore](.skillignore) excludes patterns (e.g. `ci-scripts`, `_internal-*`) from skill discovery; `.skillignore.local` is the personal, gitignored override.
- **SKILL.md frontmatter** must include `name` and `description`. When adding a local skill, namespace `name` (the team uses an `elf-`/`acme-` style prefix) to avoid collisions across tracked repos — `skillshare sync` warns on duplicate names.
- After a first push or remote change, confirm the remote actually received it with `git ls-remote origin` — a clean push log isn't proof.
