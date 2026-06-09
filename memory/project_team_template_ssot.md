---
name: team-project-template-ssot
description: E:\source\team-project-template 是團隊範本與規範單一事實來源;org-skills 等 repo 需對齊其 git workflow / commit / LF / CLAUDE.md 風格
metadata: 
  node_type: memory
  type: project
  originSessionId: c61133d1-a7d3-412d-8322-9a6f5bb34cbc
---

**事實**:`E:\source\team-project-template` 是 Elf Express 團隊的專案範本兼規範單一事實來源(SSOT)。新專案由此範本建立,org-skills 等既有 repo 也對齊它。pnpm + turbo monorepo;技術棧 Vue3+TS+Vite / .NET8+Furion+SqlSugar / Flutter / Docker。

**Why**:讓任何 AI session 在團隊各 repo 產出一致的流程與程式碼;規範「資產化」—— 每條規則都有對應的機械化執行層(編輯器 / git / CI),不靠人類記憶。

**How to apply**(動手前先讀範本對應檔):
- **CLAUDE.md 風格**:繁體中文、結論先行、useful-first、§0–14 編號結構;規範本體在 `docs/rule/`,CLAUDE.md 只做摘要 + 索引。
- **Git workflow**:開分支(`feature/fix/docs/chore/<kebab>`)→ Conventional Commits(`<type>(<scope>): <subject>` ≤50 字、動詞開頭)→ PR → **Squash** 進 `main`;`main`→`release` 用 merge commit 自動 tag。**禁止直推 / force push `main`**。
- **治理路徑**(`.github/`、`docs/rule/`、`CODEOWNERS`):需 Tech Lead approve、commit 前綴 `docs(spec):`、**AI 不自行 merge**。
- **LF 三層**:`.gitattributes`(LF 預設,`.bat/.cmd/.ps1`=CRLF)+ `.editorconfig` + `.vscode/settings.json` + CI 的 `.github/scripts/check-line-endings.mjs`。本機驗證用 `git ls-files --eol` 看 `i/`(blob),**勿在 Windows 直接跑該 .mjs**(讀工作區位元組會誤報)。
- **反 AI 騙 AI**:`scripts/verify/`(L1 manifest SHA256 / L2 RUN_TOKEN / L3 重跑對照);宣稱通過前要附 raw output。
- **協作偏好**(§11):繁中、不擅自越權、verify-before-claim、Windows 路徑用小寫 drive+冒號+正斜線。

org-skills 實際:預設分支 `main`、remote `elf-express/org-skills`、已用「分支→PR→不自 merge」流程(見 PR #1)。相關:[[org-skills 倉庫的目的與架構]]、[[首次 push 後用 git ls-remote 驗證]]
