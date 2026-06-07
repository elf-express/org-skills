# Project Skills

> Source: https://skillshare.runkids.cc/docs/understand/project-skills

---

# 專案技能



在專案層級運行技能共享——技能範圍僅限於單一儲存庫，透過 git 共享。

這什麼時候重要？

當您的團隊需要特定於儲存庫的 AI 指令（編碼標準、部署指南、API 約定）時，請使用專案技能，而這些指令不應包含在您的個人全域技能集合中。



## 使用場景



|場景 |範例|
| --- | --- |
| Monorepo 入門 |新開發人員複製儲存庫，執行 Skillshare install -p && Skillshare Syn — 即時專案上下文 |
| API 約定 |將 API 風格指南作為技能嵌入，以便每個 AI 助理都遵循團隊約定 |
|特定領域的上下文 |具有監管規則的金融應用程式、具有合規指南的醫療保健應用程式 |
|專案工具| CI/CD 部署知識、測試模式、特定於此儲存庫的遷移腳本 |
|入職加速 | “auth 這裡是如何運作的？” - 人工智慧已經從承諾的專案技能中知道了 |
|開源專案 |維護者提交 .skillshare/，以便貢獻者在克隆上獲得專案特定的 AI 上下文 |
|社區技能策展 |儲存庫的 config.yaml Skills: 部分用作策劃的技能清單 - 任何人都可以安裝 -p 以獲得相同的設定 |



---



## 概述



同步

同步

同步

.skillshare/技能/
（專案來源－致力於git）

我的技能/ 遠距技能/

.克勞德/技能

.遊標/技能

習慣/技能



---



## 自動偵測



當目前目錄中存在`.skillshare/config.yaml`時，skillshare會自動進入專案模式：



```
cd my-project/           # Has .skillshare/config.yaml
skillshare sync          # → Project mode (auto-detected)
skillshare status        # → Project mode (auto-detected)
```

零配置

只需將 `cd` 插入任何帶有 `.skillshare/` 的項目即可 — Skillshare 會自動檢測到它。沒有標誌，沒有環境變量，不需要配置。



強制使用特定模式：



```
skillshare sync -p       # Force project mode
skillshare sync -g       # Force global mode
```



---



## 全域與項目



| |全球模式|專案模式 |
| --- | --- | --- |
|來源 | 〜/.config/skillshare/skills/| .skillshare/skills/（專案根目錄）|
|配置 | 〜/.config/skillshare/config.yaml | .skillshare/config.yaml |
|目標 |系統範圍的 AI CLI 目錄 |每個專案目錄 |
|同步模式 |合併、複製或符號連結（每個目標）|合併、複製或符號連結（每個目標，預設合併）|
|追蹤倉庫 |支援（--track）|支援（--track -p）|
| Git 整合 |可選（推/拉）|直接致力於專案回購的技能 |
|範圍 |機器上的所有項目 |單一儲存庫 |



---



## `.skillshare/` 目錄結構



```
<project-root>/├── .skillshare/│   ├── config.yaml              # Targets + settings (incl. extras)│   ├── skills/.metadata.json     # Runtime metadata (hashes, timestamps — auto-managed, gitignored)│   ├── .gitignore               # Ignores logs/, trash/, backups/, and cloned remote/tracked skill dirs│   ├── extras/                  # Extras source directories│   │   └── rules/               # e.g. extras init rules --target .claude/rules -p│   │       └── coding.md│   └── skills/│       ├── my-local-skill/      # Created manually or via `skillshare new`│       │   └── SKILL.md│       ├── remote-skill/        # Installed via `skillshare install -p`│       │   └── SKILL.md│       ├── tools/               # Category folder (via --into tools)│       │   └── pdf/             # Installed via `skillshare install ... --into tools -p`│       │       └── SKILL.md│       └── _team-skills/        # Installed via `skillshare install --track -p`│           ├── .git/            # Git history preserved│           ├── frontend/ui/│           └── backend/api/├── .claude/│   └── skills/│       ├── my-local-skill → ../../.skillshare/skills/my-local-skill│       ├── remote-skill → ../../.skillshare/skills/remote-skill│       ├── tools__pdf → ../../.skillshare/skills/tools/pdf│       ├── _team-skills__frontend__ui → ../../.skillshare/skills/_team-skills/frontend/ui│       └── _team-skills__backend__api → ../../.skillshare/skills/_team-skills/backend/api└── .cursor/    └── skills/        └── (same symlink structure as .claude/skills/)
```



項目模式下的符號連結使用**相對路徑**（例如，`../../.skillshare/skills/...`）。這使得專案目錄可移植——重命名、移動它或將其克隆到另一台電腦上，並且所有符號連結都將繼續工作。全域模式使用絕對路徑，因為來源和目標位於不同的檔案系統位置。



---



## 配置格式



`.skillshare/config.yaml`：



```
targets:  - claude                    # Known target (uses default path)  - cursor                         # Known target  - name: custom-ide               # Custom target with explicit path    path: ./tools/ide/skills    mode: symlink                  # Optional: "merge" (default), "copy", or "symlink"  - name: codex                    # Optional filters (merge mode)    include: [codex-*]    exclude: [codex-experimental-*]
```



**目標**支援兩種格式：



- **短**：只是目標名稱（例如，`claude`）。使用已知的預設路徑、合併模式。
- **長**：具有 `name`、可選 `path`、可選 `mode`（`merge`、`copy` 或 `symlink`）以及可選 `include`/⧟鏡的物件。支援相對路徑（從專案根解析）和`~`擴展。



遠程技能依賴關係在`config.yaml`下的`skills:`中宣告：



```
targets:  - claude  - cursorskills:  - name: pdf    source: anthropic/skills/pdf  - name: _team-skills    source: github.com/team/skills    tracked: true  - name: review    source: github.com/team/skills/code-review    group: frontend
```



**技能**列表僅聲明遠端安裝。本地技能不需要在這裡輸入。



- `tracked: true`：搭配 `--track` 一起安裝（保留 `.git/` 的 git 儲存庫）。當有人運行 `skillshare install -p` 時，追蹤的技能將使用完整的 git 歷史記錄進行克隆，因此 `skillshare update` 可以正常工作。
- `group`：子目錄路徑（對應安裝時的`--into`）。



運行時元資料（安裝時間戳記、檔案雜湊、提交 SHA）單獨儲存在 `.skillshare/skills/.metadata.json` 中 — 該檔案是自動管理的並且 gitignored。

手提技能清單

`config.yaml` 是陳述性技能清單。在專案中，將其提交到 git，任何人都可以運行`skillshare install -p && skillshare sync`。對於全域模式，`.metadata.json`充當清單，因為全域配置不需要透過git共享。



---



## 自訂來源目錄



預設情況下，專案模式從`.skillshare/skills/`、`.skillshare/agents/`和`.skillshare/extras/`讀取技能、代理和附加內容。當您想要將技能內容與其他專案文件一起保留時，請使用可選的 `sources` 地圖覆蓋這些路徑：



```
sources:  skills: ./docs/skills  agents: ./docs/agents  extras: ./docs/extrastargets:  - claude
```



每個鍵都是可選的 - 省略一個鍵會回退到預設的 `.skillshare/<type>/` 路徑。路徑是相對於專案根目錄解析的，絕對路徑（包括`~`）也可以工作。



**常見佈局：**



```
# Co-locate skill content with existing project docssources:  skills: ./docs/skills# Keep agents in an AI-focused subdirectorysources:  agents: ./ai/agents
```



**限制：**



- **沒有帶有目標路徑的別名。 ** `skillshare sync -p` 拒絕來源解析為與目標相同的目錄（或一個包含另一個）的配置。這可以防止 `sync --force` 擦除配置的來源。例如，`sources.skills: .claude/skills` 與 `claude` 目標組合會被拒絕，並出現 `overlaps` 錯誤。
- **外部路徑跳過 gitignore 管理。 ** 當來源在專案根目錄之外（磁碟上其他位置的絕對路徑）解析時，skillshare 不會將條目新增至專案的 `.gitignore`。如有需要，請自行管理來源目錄中的忽略規則。
- **操作目錄保留在 `.skillshare/` 中。 ** 垃圾箱、備份和操作日誌始終位於 `.skillshare/` 下，無論 `sources` 設定如何。
- **`init -p`總是播種`.skillshare/{skills,agents}/`。 **自訂來源僅在您編輯`config.yaml`後生效。



---



## 模式限制



專案模式有一些有意的限制：



|特色|支援嗎？ |筆記|
| --- | --- | --- |
|合併同步模式 | ✓ |預設的、每個技能的符號連結 |
|複製同步模式| ✓ |透過 Skillshare target <name> --mode copy -p | 每個目標
|符號連結同步模式 | ✓ |透過 Skillshare target <name> --mode symlink -p | 每個目標
| --track 倉庫 | ✓ |複製到 .skillshare/skills/_repo/，加入 .gitignore（預設也會忽略 logs/、trash/ 和 backups/）|
| --發現 | ✓ |偵測新目標並將其新增至現有專案配置 |
|推/拉| ✗ |直接在專案倉庫上使用git |
|收集| ✓ |將專案目標中的本地技能收集到 .skillshare/skills/ |
|額外 | ✓ |額外同步、初始化、清單、刪除、收集 — 全部支援 -p |
|備份/復原| ✗ |不需要（專案目標是可重複的） |



---



## 何時使用：專案與組織



|需要|使用|
| --- | --- |
|特定於一個儲存庫的技能（API 樣式、部署、網域規則）|專案技能－致力於回購 |
|所有專案共享的技能（編碼標準、安全審計）|組織技能 - 透過 --track 追蹤儲存庫 |
|讓新成員加入特定專案 |專案技能 — 複製 + 安裝 + 同步 |
|組織新成員入職|組織技能－一個安裝指令|
|回購情境與組織標準 |兩者都使用－它們獨立共存 |



---



## 另請參閱



- [專案設定](https://skillshare.runkids.cc/docs/how-to/sharing/project-setup) — 逐步設定指南
- [專案工作流程](https://skillshare.runkids.cc/docs/how-to/daily-tasks/project-workflow) — 日常專案模式使用
- [組織範圍的技能](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing) — 團隊範圍的共享