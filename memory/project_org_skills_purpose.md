---
name: org-skills 倉庫的目的與架構
description: elf-express/org-skills 是用 skillshare 同步至各 AI CLI 的團隊共用 skill 倉庫,目的是把團隊規範一致化
type: project
originSessionId: e842c404-0ce9-4883-b7b9-0646bed64940
---
**事實**:`elf-express/org-skills` 是 Elf Express 團隊在 2026-05 建立的共用 skill 倉庫,預設分支 `main`。透過 [skillshare](https://skillshare.runkids.cc) 的 `--track` 機制安裝,自動同步至團隊成員的 Claude Code / Codex / Cursor 等 AI CLI。

**Why**:用戶想把團隊既有規範(前端 / 後端 / 測試)從散落各處的個人筆記與口耳相傳,固化為單一真理來源,讓 AI CLI 每次都按同樣標準產出。

**How to apply**:
- GitHub:`https://github.com/elf-express/org-skills`;預設分支 **`main`**(remote 已確認 `origin/HEAD → origin/main`)。
- **不要記絕對路徑**:工作目錄由執行環境提供(每台機器不同),指 repo 內位置一律用「相對 repo 根目錄」的相對路徑(如 `.skillshare/config.yaml`、`memory/`)。機器/個人專屬的東西放不進版控的 `.local` 檔或本機 auto-memory。
- 倉庫性質:這是 **skillshare 派發倉**,非應用程式(無 build / test / lint);架構是 config → cache → targets 三層,詳見 [CLAUDE.md](../CLAUDE.md)。
- 本地自製 skill:現況為 `.skillshare/skills/{api,deploy,ui}`(`name` 分別 `acme-api` / `acme-deploy` / `acme-ui`)。新增本地 skill 時 `name` 加前綴(團隊用 `acme-` / `elf-` 風格)避免跨 repo 撞名。
- 設計記錄寫在 `docs/superpowers/specs/`(複數,刻意與 `superpowers` 工具區隔);skillshare 官方文件機翻快照在 `docs/wiki/`,見 [[project_wiki_docs_to_mcp]]。
- 規範對齊:git workflow / commit / LF / CLAUDE.md 風格的單一事實來源在團隊範本,見 [[team-project-template-ssot]]。
- 授權:CC BY-NC 4.0(© 2026 Elf Express)。<!-- 註:repo 內目前無 LICENSE 檔,此為當初宣告的授權意圖,引用前請自行確認 -->
