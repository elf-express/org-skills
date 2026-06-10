# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

本檔案是 Claude Code 在 **org-skills** 進行任務時的常駐指引。風格與規範對齊團隊範本 `team-project-template`(繁體中文、結論先行、規範資產化)。

> 📒 **記憶(memory)**:記憶在根目錄 [`memory/`](memory/)(`MEMORY.md` 為索引),由下面這行 `@import` 每次 session 載入。**共同記憶**源頭在團隊範本 `team-project-template/memory/`,本 repo 留鏡像;其餘為 org-skills 專案特有。(Claude 各機器的自動 auto-memory 另存本地,設計上不進版控。)

@memory/MEMORY.md

---

## 0. 本專案性質

`elf-express/org-skills` 是 **[skillshare](https://github.com/runkids/skillshare) 管理的「skill 派發倉庫」**,不是應用程式。它把各上游 repo 與團隊自製的 AI skill(`SKILL.md`)彙整後,同步到各家 AI CLI 的目錄(`.adal/`、`.claude/`、`.codex/`、`.agents/`)。

- ❌ 沒有 `package.json`、沒有 build / test / lint。**不要找建置系統。**
- ✅ **skillshare CLI 是建置工具,git 是派發機制。**

---

## 1. 常用指令

於 repo 根目錄執行;多數指令支援 `--dry-run` / `-n` 預覽、`--help` / `-h`。

```bash
skillshare sync              # 依 config.yaml 重新產生所有 target(等同「build」)
skillshare sync --dry-run    # 預覽 sync 會改什麼
skillshare status            # 顯示哪裡未同步
skillshare diff              # source 與 target 差異
skillshare list              # 列出所有 skill
skillshare audit             # 安全掃描(config 設 CRITICAL 即擋 sync)
skillshare doctor            # 診斷壞掉的 skill / symlink
skillshare update --all      # 更新 tracked 來源後再 sync
skillshare new <name>        # 建立新的本地 skill
```

- **沒有傳統的「跑單一測試」**;最接近的驗證是 `skillshare sync --dry-run`、`skillshare audit`、`skillshare doctor`。
- **換行符檢查**(CI 也會跑):`node .github/scripts/check-line-endings.mjs`
- 完整 skillshare 指令參考鏡像於 [docs/wiki/54 Commands.md](docs/wiki/54%20Commands.md)。

git:預設分支 **`main`**,remote `elf-express/org-skills`。`skillshare commit` / `push` / `pull` 是會順帶 sync 的 git 包裝,純 `git` 亦可。

---

## 2. 架構地圖

**config → cache → targets** 三層:

1. **設定層** — [.skillshare/config.yaml](.skillshare/config.yaml) 是**單一事實來源(SSOT)**,宣告 `targets`(同步到哪些 CLI)與 `skills`(來源)。[.skillshare/skills/.metadata.json](.skillshare/skills/.metadata.json) 記錄每個來源解析到的版本 / hash。
2. **來源快取** — [.skillshare/skills/](.skillshare/skills/)`<group>/…/SKILL.md` 存實際內容:clone 下來的上游 + 團隊自製本地 skill(如 `api/`=`acme-api`、`deploy/`=`acme-deploy`、`ui/`)。
3. **目標層** — 各 target 目錄(`.adal/skills/`、`.claude/skills/`、`.codex/skills/`、`.agents/skills/`)是一堆指回 cache 的 **symlink**,檔名以 `__` 攤平、前綴 `<group>___` 保留來源。每個 target 的 manifest 位在 **`<target>/skills/.skillshare-manifest.json`**(例:[.claude/skills/.skillshare-manifest.json](.claude/skills/.skillshare-manifest.json)),記錄 skillshare 管的每條 symlink。`.claude/settings.json`(Claude Code 設定)有版控、非 symlink、不由 sync 產生,改它不受「鐵則」限制。

>  **鐵則:改東西改在 `config.yaml` + cache / 上游,然後 `skillshare sync`。`.adal/`、`.claude/`、`.codex/`、`.agents/` 是產生物(symlink),手改會被覆蓋、或穿過 link 改到 cache。**

target 對應(來自 config.yaml):`adal`→`.adal/`、`claude`(merge 模式)→`.claude/`、`xcode-codex`→`.codex/`、`universal`→`.agents/`。tracked 來源(`tracked: true` + `branch`,如 `_superpowers`、`_affaan-m`)是整包跟分支、用 `skillshare update` 更新;未 tracked 的是釘住的單一 skill 快照。

> Claude Code 顯示的 skill 清單同時來自 專案 + 使用者層(`~/.claude/skills/`)+ plugin,**不是每個都來自本 repo**;本 repo 的 skill 以 `config.yaml` / cache 為準。

---

## 3. 文件(docs/)

- [docs/wiki/](docs/wiki/) — `01`–`58`,skillshare 官方文件的**機翻唯讀快照**。內文是機器翻譯(常不通順),fenced code block 維持英文且較可靠。**不要編輯翻譯內文**(逐字快照,預計編成 MCP),當 skillshare 參考即可。
- [docs/superpowers/specs/](docs/superpowers/specs/) — 本 repo 變更的設計紀錄,檔名 `YYYY-MM-DD-<主題>.md`。(註:團隊範本用單數 `docs/superpower/{plan,spec}`,本 repo 實際是複數 `docs/superpowers/specs/`;引用前先 Glob 確認。)

---

## 4. 慣例與規範

- **換行符**:[.gitattributes](.gitattributes) 預設 `eol=lf`;Windows 腳本 `.bat` / `.cmd` / `.ps1` 為 `eol=crlf`。[.editorconfig](.editorconfig) 與 [.vscode/settings.json](.vscode/settings.json) 在編輯器端同步此規則,CI 的 [check-line-endings.mjs](.github/scripts/check-line-endings.mjs) 是守門。⚠️ CI 看的是「commit 進去的 blob 換行」;Windows 工作區即使顯示 CRLF,只要 blob 是 LF(`git ls-files --eol` 看 `i/`)就 OK。
- **選擇性追蹤產生物**:[.gitignore](.gitignore) 忽略 `.adal/skills/` 與 `.skillshare/skills/` 下的 `_*/` 群組目錄,但**群組命名**的輸出(`superpowers___*`、`affaan-m__*`)有追蹤。改 synced 檔前先看 .gitignore 確認它在不在 git 內。
- **探索過濾**:[.skillignore](.skillignore) 排除某些 pattern 不被當 skill;`.skillignore.local` 是個人覆寫(已 gitignore)。
- **SKILL.md frontmatter** 必含 `name` 與 `description`;新增本地 skill 時 `name` 加前綴(團隊用 `elf-` / `acme-` 風格)避免跨 repo 撞名,`skillshare sync` 撞名會警告。

---

## 5. 與 Claude 協作偏好(對齊團隊範本 §11)

- **語言**:溝通與 commit 訊息預設**繁體中文**。
- **回覆**:精簡、結論先行;檔案引用用 `[檔名](路徑#L行號)`。
- **不擅自越權**:不主動 `git push --force` / `git reset --hard` / 刪分支;commit / push 前先確認(尤其推到團隊共用 remote)。
- **Verify-before-claim**:宣稱「OK / 通過 / 修好 / 不存在」前,要有對應 tool 的 raw output 為證(例:push 後用 `git ls-remote` 對 local HEAD)。
- **Windows + MSYS bash 路徑**:用小寫 drive + 冒號 + 正斜線(`e:/source/org-skills`);不確定先 Glob,不要硬猜。
