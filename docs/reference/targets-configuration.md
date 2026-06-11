# Targets / Configuration(skillshare 官方參考 + schema 驗證)

> **來源**:<https://skillshare.runkids.cc/docs/reference/targets/configuration>
> **Schema(最權威)**:<https://raw.githubusercontent.com/runkids/skillshare/main/schemas/project-config.schema.json>
> **抓取 / 驗證日期**:2026-06-11
> **用途**:org-skills `.skillshare/config.yaml` 的 target / skill 設定權威依據。
>
> 本檔與 [`docs/wiki/`](../wiki/)(機翻唯讀快照 01–58)不同:這裡放**官方原文 + 我們的 schema 驗證結論**,可隨官方更新而更新。

---

## ⚠️ 最重要結論:target 有 legacy 與 modern 兩種寫法,org-skills 用的是 modern(正確)

依 **project-config JSON schema**(`config.yaml` 第一行就引用它,比文檔頁更權威)驗證:

| 寫法 | 形式 | schema 定位 |
|---|---|---|
| **legacy(舊式)** | `mode` / `include` / `exclude` **直接**放 target 下 | 仍可用,但 schema 標為 *legacy* |
| **modern(新式,推薦)** | 包進 **`skills:` / `agents:`** 子鍵 | schema 推薦;可分別設定 skill 與 agent |

> 官方那頁 reference 的 project config 範例用的是 **legacy** 寫法(尚未更新到新格式)。
> **org-skills 當前 config 用的 `skills:` 子鍵正是 modern 寫法 → 是對的,不用改。**

### target 物件允許的欄位(schema `additionalProperties: false`)

- `name` — **必需**
- `path` / `mode` / `include` / `exclude` — **legacy**,直接放 target 下
- `skills`(**modern**)— 子欄位:`path` / `mode` / `target_naming` / `include` / `exclude`
- `agents`(**modern**)— 子欄位:`path` / `mode` / `include` / `exclude`
- ⚠️ `target_naming` **只能放 `skills:` 下**,不能當 target 的直接欄位

### org-skills 當前寫法(✅ 正確,modern)

```yaml
targets:
  - name: claude
    skills:
      mode: merge
      target_naming: flat
  - name: xcode-codex
    skills:
      target_naming: flat
  - name: opencode
    skills:
      target_naming: flat
```

---

## 欄位速查(繁中)

- **mode**:`merge`(預設,逐 skill symlink、保留本地 skill)/ `copy`(實體檔,給不能跟 symlink 的 CLI)/ `symlink`(整個目錄一個 link)
- **target_naming**:`flat`(預設,巢狀攤平成 `a__b`)/ `standard`(用 SKILL.md 的 `name`,遵循 Agent Skills 規範)
- **include / exclude**:Go `filepath.Match` 語法(`*` `?` `[...]`);`include` 先套用、`exclude` 後套用;對「攤平後的名字」比對;symlink 模式忽略
- **skills(陣列)**:`name`(必需)+ `source`(必需)+ `tracked`(選填,`--track` 安裝時為 true);由 install/uninstall 自動維護
- **audit.block_threshold**:`CRITICAL`(預設,掃到即擋 sync)/ HIGH / MEDIUM / LOW / INFO

---

## 官方原文(reference,英文為主以維持可靠)

### Overview

Skillshare uses a YAML-based configuration system with locations at `~/.config/skillshare/config.yaml` (global) and `.skillshare/config.yaml` (project).

```
~/.config/skillshare/
├── config.yaml
├── skills/
│   ├── .metadata.json
│   ├── my-skill/
│   └── _team-repo/
├── extras/
│   └── rules/
└── ~/.local/share/skillshare/backups/
```

### IDE Support

Add as the first line of the config for autocompletion + validation:

- **Global:** `# yaml-language-server: $schema=https://raw.githubusercontent.com/runkids/skillshare/main/schemas/config.schema.json`
- **Project:** `# yaml-language-server: $schema=https://raw.githubusercontent.com/runkids/skillshare/main/schemas/project-config.schema.json`

### Core Configuration Fields

#### `source`
Path to skills directory (single source of truth). Default: `~/.config/skillshare/skills`

#### `mode`
Default sync mode for all targets:
- `merge` (default) — Individual skill symlinks, local skills preserved
- `copy` — Real files, for CLIs that cannot follow symlinks
- `symlink` — Entire target directory as one symlink

#### `target_naming`
Naming strategy for merge/copy sync:
- `flat` (default) — Nested skills flattened with `__` separators (e.g., `frontend__dev`)
- `standard` — Uses SKILL.md `name` field directly, following Agent Skills specification

#### `targets` (global, map form)

```yaml
targets:
  claude:
    path: ~/.claude/skills
  codex:
    path: ~/.codex/skills
    mode: symlink
  custom:
    path: ~/my-app/skills
    mode: copy
    include: [codex-*]
    exclude: [experimental-*]
```

#### `include` / `exclude` (Target Filters)

Control which skills sync in merge and copy modes. Matching uses Go `filepath.Match` syntax (`*`, `?`, `[...]`):

- `include` applied first; `exclude` applied after
- Matching against flat target names (e.g., `team__frontend__ui`)
- Ignored in symlink mode
- Excluded symlinks removed from targets; local non-symlink folders preserved

Pattern examples: `codex-*` (prefix), `team__*` (namespace), `*-experimental` (suffix), `core-?` (single char), `[ab]-tool` (small set).

CLI management:
```
skillshare target claude --add-include "team-*"
skillshare target claude --add-exclude "_legacy*"
skillshare sync
```

#### `skills`
Tracks remotely-installed skills (auto-managed by install/uninstall):

```yaml
skills:
  - name: pdf
    source: anthropics/skills/skills/pdf
  - name: _team-skills
    source: github.com/team/skills
    tracked: true
```

Fields: `name` (required), `source` (required), `tracked` (optional, true if installed with `--track`). Since v0.16.2, installation metadata lives in centralized `.metadata.json`.

#### `agents_source`
Custom source directory for agents (overrides default `~/.config/skillshare/agents/`). Global mode only; project mode uses `.skillshare/agents/`.

#### `extras`
Non-skill resources (rules, commands, prompts) synced to arbitrary directories:

```yaml
extras_source: ~/my-extras
extras:
  - name: rules
    source: ~/company-shared/rules
    targets:
      - path: ~/.claude/rules
      - path: ~/.cursor/rules
        mode: copy
  - name: agents
    targets:
      - path: ~/.claude/agents
        flatten: true
```

Per-target fields: `path` (required), `mode` (`merge`/`copy`/`symlink`, default `merge`), `flatten` (optional). Source resolution: per-extra `source` → `extras_source/<name>/` → `~/.config/skillshare/extras/<name>/`.

#### `ignore`
Glob patterns to skip during sync. Default: `**/.DS_Store`, `**/.git/**`

#### `gitlab_hosts` / `azure_hosts`
Custom self-managed GitLab / Azure DevOps hostnames. Also via env `SKILLSHARE_GITLAB_HOSTS` / `SKILLSHARE_AZURE_HOSTS`.

#### `audit`

```yaml
audit:
  block_threshold: CRITICAL
  profile: default
  dedupe_mode: global
  enabled_analyzers: [static, dataflow, tier, integrity]
```

Fields: `block_threshold` (CRITICAL/HIGH/MEDIUM/LOW/INFO, default CRITICAL), `profile` (default/strict/permissive), `dedupe_mode` (legacy/global, default global), `enabled_analyzers`. Profiles: `default` (CRITICAL/global), `strict` (HIGH/global), `permissive` (CRITICAL/legacy). Analyzer IDs: `static`, `dataflow`, `tier`, `integrity`, `structure`, `cross-skill`. Precedence: CLI flags → project config → global config → profile defaults.

#### `context_budget`

```yaml
context_budget:
  warn_always_loaded_tokens: 10000
  warn_on_demand_tokens: 100000
```

Defaults: 10000 / 100000. Set to 0 to disable.

#### `preserve_tilde_on_save`
When true, folds `$HOME` back to `~` before writing config.yaml (portable dotfiles). Default `false`. Global config only.

#### `git_root`
Which directory `skillshare commit`/`push`/`pull` operate on. Global only. Values: `skills` (default), `agents`, `extras`, `root`.

### Project Config (`.skillshare/config.yaml`)

```yaml
targets:
  - claude                          # String: known target
  - cursor
  - name: custom-ide                # Object: custom path/mode
    path: ./tools/ide/skills
    mode: symlink
  - name: codex
    include: [codex-*]
    exclude: [codex-experimental-*]

skills:
  - name: pdf
    source: anthropic/skills/pdf
  - name: _team-skills
    source: github.com/team/skills
    tracked: true

audit:
  block_threshold: HIGH
  profile: strict
```

> ☝️ 注意:官方這段 project 範例的 target 用的是 **legacy**(`mode`/`include` 直接放 target)。新式請改用 `skills:` 子鍵 —— 見本檔最上方結論。

### Managing Configuration

- View: `skillshare status`
- Edit then sync: `$EDITOR .skillshare/config.yaml && skillshare sync`
- Reset: `rm config.yaml && skillshare init`

### Environment Variables

- `SKILLSHARE_CONFIG` — override config file path
- `GITHUB_TOKEN` — API rate limits
- `SKILLSHARE_GITLAB_HOSTS` / `SKILLSHARE_AZURE_HOSTS` — comma-separated hostnames

### Platform Differences

- **macOS/Linux:** symlinks
- **Windows:** NTFS junctions (no admin required)

---

## 與本 repo 的關聯

- 本 repo 是 **project 模式**(`.skillshare/config.yaml`),target 用 **list 形式**(字串或物件)。
- 三個 target:`claude` / `xcode-codex`(Codex) / `opencode`,皆 `skills.target_naming: flat`。
- 鐵則仍適用:改設定改 `config.yaml` + cache,再 `skillshare sync`;`.claude/` `.codex/` `.opencode/` 是產生物。詳見 [CLAUDE.md](../../CLAUDE.md)。
