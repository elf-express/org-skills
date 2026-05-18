# elf-express / org-skills

Organization-wide AI skills for the **elf-express** team — distributed via [skillshare](https://github.com/runkids/skillshare).

This repo ships:

- **Custom skills** (this repo)
  - `frontend/ui` — Frontend UI patterns
  - `backend/api` — Backend API patterns
  - `devops/deploy` — Deployment patterns
- **Vendored skills** (mirrored from upstream, under `.adal/skills/`)
  - `algorithmic-art`, `brand-guidelines`, `canvas-design` (from [anthropics/skills](https://github.com/anthropics/skills))

---

## For Team Members — Install

> Requires [skillshare](https://github.com/runkids/skillshare) CLI.

### One-time setup (recommended: tracked install)

```powershell
skillshare install github.com/elf-express/org-skills --track
skillshare sync
```

### Only need a subset?

```powershell
skillshare install github.com/elf-express/org-skills --all --exclude devops-deploy
```

### Daily — keep up to date

```powershell
skillshare update --all
skillshare sync
```

---

## Repo Layout

```
org-skills/
├── frontend/
│   └── ui/SKILL.md            ──►  flattened to: _org-skills__frontend__ui
├── backend/
│   └── api/SKILL.md           ──►  flattened to: _org-skills__backend__api
├── devops/
│   └── deploy/SKILL.md        ──►  flattened to: _org-skills__devops__deploy
├── .adal/skills/
│   ├── algorithmic-art/       (vendored from anthropics/skills)
│   ├── brand-guidelines/      (vendored from anthropics/skills)
│   └── canvas-design/         (vendored from anthropics/skills)
├── .skillignore               (exclude internal/CI helpers from discovery)
└── .skillshare/config.yaml    (project-mode config, for repo maintainers)
```

skillshare auto-flattens nested folders so any AI CLI sees a flat skill list. The `_org-skills__` prefix preserves origin path for traceability.

---

## Excluding skills from discovery

Add patterns to `.skillignore` at the repo root:

```
# CI/CD helpers — not installable skills
ci-scripts
_internal-*
```

Individual developers can locally override with `.skillignore.local` (already gitignored):

```
!_internal-my-tool
```

---

## For Maintainers

### Add a new skill

1. Create folder: `<category>/<skill-name>/SKILL.md`
2. SKILL.md frontmatter must include `name` and `description`:
   ```yaml
   ---
   name: my-skill
   description: What this skill does
   ---
   ```
3. Commit + push.

### Naming / collision avoidance

When two skills share the same `name`, `skillshare sync` warns. Either:
- Namespace the `name` field (e.g. `elf-frontend-ui`), or
- Route with target `include`/`exclude` filters in consumer configs.

### Private clones

SSH (dev machines):

```powershell
skillshare install git@github.com:elf-express/org-skills.git --track
```

HTTPS + token (CI):

```powershell
$env:GITHUB_TOKEN = "ghp_xxx"
skillshare install https://github.com/elf-express/org-skills.git --track
```

---

## Best Practices

**Team leads**
- Organize by function (`frontend/`, `backend/`, `devops/`)
- Namespace skill `name` fields to avoid collisions
- Tag stable releases for version pinning

**Team members**
- Run `skillshare update --all` daily
- Report broken skills in this repo''s Issues
- Open PRs to improve / add skills