# docs/wiki 重新編號 + 補表格/整理 — 設計

> 日期：2026-06-08 ｜ 範圍：`docs/wiki/`（GitHub Wiki 頁面，目前未追蹤）

## 問題

`docs/wiki/` 是 skillshare 文件站（skillshare.runkids.cc）抓取 + 機器翻譯的快照，目前有三個批次、編號互相衝突：

- 指南批次：`01Introduction` … `13How`（01–13）
- 參考批次：`02Skill Discovery` … `45list`（02–45）
- 新增單檔：`01Daily Workflow`

GitHub Wiki 以檔名數字前綴決定側欄順序，重複編號會破壞排序。另外抓取造成兩類結構毀損：
1. **表格/流程被打散**成多行單詞（例：Daily Workflow「概述」→ 編輯/同步/犯罪/推/偏僻的/來源/目標/拉）。
2. **程式碼區塊被擠成一行**（例：`# Start of dayskillshare pullskillshare status...`）。
3. **標題被截斷**：抓取器在第一個 `-` 切斷、並去掉 `:`，導致 `AI-Assisted Development`→`AI`、`Cross-Machine Sync`→`Cross`、`Recipe: Pre-commit Hook`→`Recipe Pre` 等（檔名與 `# H1` 都被截）。

## 目標

1. 合併成單一連續序列 **01–58**，檔名格式統一為 `NN 標題.md`（數字 + 一個空格 + 標題）。
2. 補回被打散的表格、修復被擠成一行的程式碼區塊。
3. 補全被截斷的檔名（及對應 H1）。

## 鐵則：不改原文

所有現有字句（含機器翻譯，如「犯罪」=commit、「偏僻的」=remote）**一律保留**。只還原結構、補全被截斷的標題，不重寫、不重譯、不修正譯文用詞。

## 編號 / 改名對照（58 檔）

格式 `NN 標題.md`。★ = 補全的截斷標題（檔名 + H1 一起補）。

### 指南批次（編號不變）
| 現在 | → 之後 |
|------|--------|
| 01Introduction | 01 Introduction |
| 02Getting Started | 02 Getting Started |
| 03First Sync | 03 First Sync |
| 04From Existing Skills | 04 From Existing Skills |
| 05Quick Reference | 05 Quick Reference |
| 06Using skillshare with Claude Code | 06 Using skillshare with Claude Code |
| 07Using skillshare with GitHub Copilot | 07 Using skillshare with GitHub Copilot |
| 08Using skillshare with Codex | 08 Using skillshare with Codex |
| 09Using skillshare with Multiple AI Tools | 09 Using skillshare with Multiple AI Tools |
| 10Using skillshare in Dev Containers | 10 Using skillshare in Dev Containers |
| 11Try skillshare in the Playground | 11 Try skillshare in the Playground |
| 12AI ★ | 12 AI-Assisted Development |
| 13How ★ | 13 How-To Guides |

### 新增檔
| 現在 | → 之後 |
|------|--------|
| 01Daily Workflow | 14 Daily Workflow |

### 參考批次（一律 +13）
| 現在 | → 之後 |
|------|--------|
| 02Skill Discovery | 15 Skill Discovery |
| 03Backup & Restore | 16 Backup & Restore |
| 04Project Workflow | 17 Project Workflow |
| 05Creating Skills | 18 Creating Skills |
| 06Filtering Skills | 19 Filtering Skills |
| 07Organizing Skills with Folders | 20 Organizing Skills with Folders |
| 08Best Practices | 21 Best Practices |
| 09Project Setup | 22 Project Setup |
| 10Organization | 23 Organization |
| 11Cross ★ | 24 Cross-Machine Sync |
| 12Hub Index Guide | 25 Hub Index Guide |
| 13Migration | 26 Migration |
| 14Centralized vs Local | 27 Centralized vs Local |
| 15Docker Test, Develop, and Deploy | 28 Docker Test, Develop, and Deploy |
| 16Securing Your Skills | 29 Securing Your Skills |
| 17Recipes | 30 Recipes |
| 18Recipe CICD Skill Validation | 31 Recipe CICD Skill Validation |
| 19Recipe Pre ★ | 32 Recipe Pre-commit Hook |
| 20Recipe Private Enterprise Skills | 33 Recipe Private Enterprise Skills |
| 21Recipe Project Mode Workflow | 34 Recipe Project Mode Workflow |
| 22Recipe Cross ★ | 35 Recipe Cross-Machine Sync |
| 23Recipe Team Onboarding | 36 Recipe Team Onboarding |
| 24Centralized Skills Repo | 37 Centralized Skills Repo |
| 25Understand | 38 Understand |
| 26Source & Targets | 39 Source & Targets |
| 27Sync Modes | 40 Sync Modes |
| 28Tracked Repositories | 41 Tracked Repositories |
| 29Skill Format | 42 Skill Format |
| 30Agents | 43 Agents |
| 31Project Skills | 44 Project Skills |
| 32Declarative Skill Manifest | 45 Declarative Skill Manifest |
| 33Audit Engine | 46 Audit Engine |
| 34Why Local ★ | 47 Why Local-First |
| 35Security | 48 Security |
| 36Comparing Skill Management Approaches | 49 Comparing Skill Management Approaches |
| 37Skill Design | 50 Skill Design |
| 38Skill Design Patterns | 51 Skill Design Patterns |
| 39Sync Modes Explained | 52 Sync Modes Explained |
| 40Reference | 53 Reference |
| 41Commands | 54 Commands |
| 42init | 55 init |
| 43install | 56 install |
| 44uninstall | 57 uninstall |
| 45list | 58 list |

## 執行：兩階段

### 第一階段 — 改名/編號（確定、可逆、低風險）
- 參考批次老 `N` → 新 `N+13`。為避免覆蓋既有檔，**以老編號降冪**處理（先 45→58、44→57…直到 02→15）；每個目標槽位都已先被騰空。
- `01Daily Workflow` → `14 Daily Workflow` 需在 `14Centralized vs Local`（→27）騰空後再做。
- 指南批次只在數字後補空格；`12AI`、`13How` 同時補全標題。
- 檔案目前未被 git 追蹤，用一般檔案搬移即可（非 `git mv`）。
- 無跨檔內部連結需修正（「另請參閱」連到 skillshare.runkids.cc，非同層檔）。

### 第二階段 — 內容修復（只動有壞掉結構的檔）
逐檔處理，僅在偵測到毀損結構時動作：
- **補表格**：被打散成多行的表格/流程 → 還原成 Markdown 表格，沿用現有譯文。結構不明時，抓該檔 `> Source:` 原始頁當基準（只取結構，不取文字）。
- **修程式碼區塊**：` ``` ` 內多道指令擠成一行 → 拆回各自一行（指令字串不變）。
- **補全 H1**：7 個截斷標題的 `# H1` 補成完整標題，與檔名一致。

## 不在範圍內
- 不重譯、不修正機器翻譯用詞。
- 不改頁面正文敘述。
- 不新增 `_Sidebar.md` / `Home.md`（除非另行要求）。
- 不動 `docs/wiki/` 以外的檔。

## 驗收
- `docs/wiki/` 恰有 58 檔，檔名 01–58 連續無重複、無缺號。
- 7 個截斷檔名與 H1 已補全。
- 抽查數檔：原本被打散的表格已成 Markdown 表格、擠成一行的程式碼區塊已拆行，且正文字句與修復前逐字相同。
