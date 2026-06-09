---
name: org-skills 倉庫的目的與架構
description: elf-express/org-skills 是用 skillshare 同步至各 AI CLI 的團隊共用 skill 倉庫,目的是把團隊規範一致化
type: project
originSessionId: e842c404-0ce9-4883-b7b9-0646bed64940
---
**事實**:`elf-express/org-skills` 是 Elf Express 團隊在 2026-05 建立的共用 skill 倉庫,主要分支 `master` / `dev` / `release`。透過 [skillshare](https://skillshare.runkids.cc) 的 `--track` 機制安裝,自動同步至團隊成員的 Claude Code / Codex / Cursor 等 AI CLI。

**Why**:用戶想把團隊既有規範(前端 / 後端 / 測試)從散落各處的個人筆記與口耳相傳,固化為單一真理來源,讓 AI CLI 每次都按同樣標準產出。

**How to apply**:
- 工作目錄:`E:\source\org-skills\`
- GitHub:`https://github.com/elf-express/org-skills`
- 分支策略:在 `dev` 開發 → merge 到 `master` → tag 後 merge 到 `release`(release 預留未來 Docker 自動打包觸發點)
- skill 命名:`elf-<kebab-case>`(避免與其他 tracked repo 衝突)
- 目錄結構:`skills/{frontend,backend,testing}/<sub>/SKILL.md` + `agents/` + `docs/superpower/{plan,spec}/`
- 授權:CC BY-NC 4.0(© 2026 Elf Express)
- 設計記錄寫在 `docs/superpower/`(單數,刻意與 `superpowers` 工具區隔)
- 首個示範 skill:`elf-sqlsugar`(backend)、`elf-allure-report` + `elf-test-e2e`(testing)
- 測試報告 UI 標準:Allure Report v3
- E2E 框架:Playwright,基線 config 在 `skills/testing/e2e/SKILL.md`
