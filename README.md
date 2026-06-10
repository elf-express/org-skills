# elf-express / org-skills

elf-express 團隊的 **AI skill 共用倉庫**,透過 [skillshare](https://github.com/runkids/skillshare) 把同一套 skill 派發到各家 AI CLI(Claude Code、OpenAI Codex、opencode)。

> **為什麼有這個 repo:全團隊共用同一套 skill**,讓每個人用 AI 產出的結果都照同一套規範——避免各自用自己的、專案風格分歧。要改規範就改這個 repo(走 PR),全隊一起同步。

---

## 這個倉庫有什麼

- **Vue / Tauri 全套**:antfu 全家桶(vue、pinia、vite、vitest、nuxt、vueuse、slidev…)+ Tauri(tauri-v2、tauri-frontend-js)
- **通用精選**:frontend-design、開發方法論(superpowers)、anthropics / runkids 官方 skill
- **團隊自製 skill**:目前無(要建用 `skillshare new`,`name` 加 `acme-` 前綴)
- **同步目標(targets)**:`claude`(Claude Code)、`xcode-codex`(OpenAI Codex)、`opencode`

---

## 團隊成員 — 訂閱

> 需要 [skillshare](https://github.com/runkids/skillshare) CLI。

### 1. 一次性訂閱(tracked)

```bash
skillshare install github.com/elf-express/org-skills --track
skillshare sync
```

私有 repo 改用 SSH:

```bash
skillshare install git@github.com:elf-express/org-skills.git --track
```

### 2. 定期保持同步(常跑)

```bash
skillshare update --all
skillshare sync
```

`update --all` 把本機的 org-skills 更新到最新的 `main`,`sync` 重新派發到你的 AI CLI——跑完,你手上的 skill 就跟團隊一致。

### 3. 要改規範?開 PR,別在本機改

在**這個 repo** 改 skill → 開 PR → 合併後,大家跑一次步驟 2 就拿到。**這是讓全隊維持同一版、不分歧的關鍵。**

---

## 常用命令

> 都在 repo 根目錄執行;多數支援 `--dry-run` / `-n` 預覽、`--help` / `-h`。本專案**沒有**傳統 build / test / lint——skillshare 就是建置工具,git 是派發機制。

| 命令 | 用途 |
|---|---|
| `skillshare status` | 看哪些 target 還沒同步 |
| `skillshare diff` | 看來源與 target 的差異 |
| `skillshare sync` | 依 `config.yaml` 重新派發(等同「build」) |
| `skillshare sync --dry-run` | 預覽 sync 會改什麼(最接近「測試」) |
| `skillshare update --all` | 更新所有 tracked 來源後再同步 |
| `skillshare search <關鍵字>` | 搜 GitHub 上含 `SKILL.md` 的 skill(可直接裝) |
| `skillshare ui start` | 開 web dashboard(背景),網址 `http://127.0.0.1:19420` |
| `skillshare install <來源> --track` | 加一個新的 tracked 來源 |
| `skillshare list` | 列出所有 skill |
| `skillshare audit` | 安全掃描(掃到 CRITICAL 會擋 sync) |
| `skillshare doctor` | 診斷壞掉的 skill / symlink |

---

## 維護者 — 加 / 改 skill

### 加團隊自製 skill

1. 用 `skillshare new <name>` 建立,或手動建 `.skillshare/skills/<name>/SKILL.md`
2. frontmatter 必含 `name` 與 `description`,`name` **加前綴**避免跨 repo 撞名:
   ```yaml
   ---
   name: acme-my-skill
   description: 這個 skill 做什麼
   ---
   ```
3. `skillshare sync` → commit → 開 PR

### 加第三方來源

在 `.skillshare/config.yaml` 的 `skills:` 加 `source:`(或用 `skillshare ui` / `skillshare search` 抓,**記得選 project**):

```yaml
  - name: vue-best-practices
    source: github.com/antfu/skills/skills/vue-best-practices
```

整包跟著分支的用 `tracked: true` + `group:`(例如 `superpowers`)。

### 排除某些檔不被當 skill

在 `.skillignore` 加 pattern;`.skillignore.local` 是個人覆寫(已 gitignore)。

### 命名 / 避免撞名

兩個 skill `name` 相同時 `skillshare sync` 會警告。團隊自製一律加前綴(`acme-` / `elf-`)。

### 私有 repo(CI 用 token)

```bash
GITHUB_TOKEN=ghp_xxx skillshare install https://github.com/elf-express/org-skills.git --track
```

---

## 鐵則

> 改東西改在 `.skillshare/config.yaml` + 上游,然後 `skillshare sync`。各 target 目錄(`.claude/`、`.codex/`、`.opencode/`)是**產生物(symlink)**,手改會被覆蓋。

更完整的架構與協作規範見 [CLAUDE.md](CLAUDE.md)。
