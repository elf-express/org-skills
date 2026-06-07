# list

> Source: https://skillshare.runkids.cc/docs/reference/commands/list

---

＃ 列表



列出來源目錄中所有已安裝的技能。



```
skillshare list              # Interactive TUI (default on TTY)
skillshare list --verbose    # Detailed plain text view
skillshare list --json       # JSON output for CI/scripts
```



## 何時使用



- 查看安裝了哪些技能以及它們來自哪裡
- 互動式搜尋和過濾技能
- 檢查哪些技能被追蹤儲存庫與本機技能
- 在清理之前審核你的技能集合



![list demo](https://skillshare.runkids.cc/assets/images/list-demo-99c965dd6f8ba936d6286b371bc85539.png)



## 互動式 TUI



在 TTY 上，`skillshare list` 啟動互動終端 UI：



- **智慧型過濾** — 按 `/` 依名稱、路徑或來源過濾。支援標籤語法進行精確過濾：



|標籤 |短|價值 |範例|
| ---| ---| ---| ---|
|類型： | t: |追蹤、遠端、本地、github | t：追蹤|
|群組：|克： |任意目錄名 | g：安全|
|回購：| : |任何倉庫名稱 | r：團隊|
|類型: | k: |技能，代理| k：代理|
|狀態： | s: |啟用、停用 | s：停用|



標籤可以與自由文字組合（AND 邏輯）：



```
t:tracked g:security audit
```



這僅顯示名稱包含“audit”的“安全”群組中追蹤的技能。

提示

對於啟用/停用的過濾，您通常不需要標籤 - 只需按`s`（請參閱下面的**狀態過濾器**）。 `s:enabled` / `s:disabled` 標籤用於與其他標籤*組合*狀態，例如`t:tracked s:disabled`。
- **狀態過濾器** — 按 `s` 循環啟用/停用視圖：**全部 → 啟用 → 停用 → 全部**。當前狀態顯示為選項卡欄旁邊的`Status:`晶片，因此在一個大列表中，您可以立即縮小到透過`.skillignore`禁用的技能（或隱藏它們）。由選項卡和`/`過濾器組成。
- **鍵盤導航** — 箭頭鍵瀏覽，`q`退出
- **詳細資料面板** — 顯示所選技能的描述、磁碟路徑、檔案和同步目標
- **啟用/停用切換** — 按`E`切換所選技能的啟用/停用狀態。無需離開 TUI 即可立即寫入`.skillignore`。停用技能在詳細資料面板中顯示紅色**停用**徽章。
- **內容檢視器** — 按 `Enter` 開啟雙窗格檢視器，左側為檔案樹，右側為 Markdown 渲染的內容。 `j`/`k`瀏覽文件（自動預覽），`l`/`Enter`展開目錄，`h`折疊。 `Ctrl+d`/`u` 捲動內容半頁，`g`/`G` 跳到頂部/底部。還支援滑鼠滾輪和單擊。



使用 `--no-tui` 跳過 TUI 並列印純文字：



```
skillshare list --no-tui          # Plain text output
skillshare list --no-tui | less   # Pipe to pager manually
```



## 搜尋和過濾



無需進入TUI即可過濾技能：



```
skillshare list react                     # Filter by name/path/source
skillshare list --type local              # Only local skills
skillshare list --type github             # Only GitHub-sourced skills
skillshare list react --sort newest       # Sort by install date
skillshare list --json | jq '.[].name'   # JSON for scripting
```

人工智慧的使用

以程式方式檢驗技能時使用`--json`模式：

```
skillshare list --json | jq '.[] | {name, source, type}'
```



## 輸出範例



### 緊湊視圖



當您使用資料夾來組織技能時，技能會自動按目錄分組：



```
Installed skills─────────────────────────────────────────  frontend/    → react-helper        github.com/user/skills    → vue-helper          github.com/user/skills  → my-skill              local  → commit-commands       github.com/user/skills  → old-draft             local  [disabled]Tracked repositories─────────────────────────────────────────  ✓ _team-skills          3 skills, up-to-date
```



如果所有技能都位於頂層（沒有資料夾），則輸出是一個平面清單 - 與先前的版本相同。



### 詳細視圖



```
skillshare list --verbose
```



```
Installed skills─────────────────────────────────────────  frontend/    react-helper      Source:      github.com/user/skills      Type:        github      Installed:   2026-01-15    vue-helper      Source:      github.com/user/skills      Type:        github      Installed:   2026-01-15  my-skill    Source:      (local - no metadata)  commit-commands    Source:      github.com/user/skills    Type:        github    Installed:   2026-01-15Tracked repositories─────────────────────────────────────────  ✓ _team-skills          3 skills, up-to-date  ! _other-repo           5 skills, has changes
```



## 全域與項目



技能共享在兩個層面上運作。 `list`指令顯示活躍等級的技能：



專案

.skillshare/技能/

列表-p

全球的

〜/.config/skillshare/技能/

列表/列表-g



| |全球|項目|
| ---| ---| ---|
|來源 | 〜/.config/skillshare/skills/| .skillshare/技能/ |
|旗幟| -g 或預設 | -p 或自動偵測 |
|範圍 |機器上的所有項目 |單一儲存庫 |
|分享透過 |推/拉| git 提交 |



### 自動偵測



當您在沒有標誌的情況下執行 `skillshare list` 時，skillshare 會自動偵測模式：



是的

不

技能共享列表

.skillshare/config.yaml 存在嗎？

專案模式

全域模式



```
cd my-project/            # Has .skillshare/config.yaml
skillshare list           # → Installed skills (project)
cd ~
skillshare list           # → Installed skills (global)
```



使用 `-p` 或 `-g` 覆蓋自動偵測：



```
skillshare list -g        # Force global, even inside a project
skillshare list -p        # Force project, even without auto-detection
```



## 專案模式



```
skillshare list          # Auto-detected if .skillshare/ exists
skillshare list -p       # Explicit project mode
```



### 範例輸出



```
Installed skills (project)─────────────────────────────────────────  tools/    → pdf               anthropic/skills/pdf    → review            github.com/team/tools  → my-skill            local→ 3 skill(s): 2 remote, 1 local
```



項目清單使用與全域清單相同的視覺格式，標題中帶有`(project)`標籤。技能按目錄分組並分類為 `local`（無元資料）或按來源 URL（遠端）。



## 選項



|旗幟|描述 |
| ---| ---|
| [圖案] |依名稱、路徑或來源過濾技能（不區分大小寫）|
| --詳細，-v |顯示詳細資料（來源、類型、安裝日期）|
| --json，-j |輸出為 JSON（對於 CI/腳本有用）|
| --no-tui |停用互動式 TUI，使用純文字輸出 |
| --type, -t <類型> |按類型過濾：追蹤、本地、github |
| --sort, -s <順序> |排序順序：名稱（預設）、最新、最舊|
| --項目，-p |列出項目技能 |
| --全局，-g |列出全球技能 |
| --幫助，-h |顯示幫助 |



## 目錄分組



當技能被組織到資料夾中時（在安裝過程中透過[`--into`](https://skillshare.runkids.cc/docs/reference/commands/install)或手動`mv` + `sync`），`list`會以目錄自動將它們分組：



```
  frontend/    → react-helper        github.com/user/skills    → vue-helper          github.com/user/skills  → my-skill              local
```



- 同一資料夾下的技能共用一個群組標題（例如`frontend/`）
- 在每個群組中，僅顯示基本名稱（而不是完整路徑）
- 頂級技能（無父資料夾）在底部顯示為未分組
- 如果**所有**技能都是頂級，則輸出是一個平面列表 - 沒有群組標題



分組基於來源目錄內的目錄結構，而不是標誌。要開始使用它，請使用`--into`組織技能：



```
skillshare install owner/repo -s react-patterns --into frontend
skillshare install owner/repo -s vue-patterns --into frontend
```



詳細內容請參考【用資料夾整理技能】(https://skillshare.runkids.cc/docs/how-to/daily-tasks/organizing-skills)。



## 了解輸出



### 技能來源



|標籤|意義|
| ---| ---|
|本地 |本地創建，無元資料 |
| github.com/... |從 GitHub 安裝 |
|追蹤： <repo> |追蹤儲存庫的一部份 |
| [已停用] |透過 .skillignore 排除技能（請參閱啟用/停用）|



### 儲存庫狀態



|圖標|意義|
| ---| ---|
| ✓ |最新的，沒有本地變更 |
| ！ |有未提交的更改 |



## 代理支援



`skillshare list agents` 僅篩選代理，顯示代理源目錄中的 `.md` 檔案（`~/.config/skillshare/agents/` 或 `.skillshare/agents/`）。



```
skillshare list agents              # List agents only
skillshare list agents --json       # JSON output for agents
skillshare list agents --verbose    # Detailed agent list
```



在互動式 TUI 中，客服人員會顯示 **[A]** 徽章以將其與技能區分開來。所有 TUI 功能（過濾、詳細資訊面板、啟用/停用切換）都以相同的方式運作。



如果沒有 `agents` 參數，`list` 僅顯示技能（預設行為）。背景請參閱【特工】(https://skillshare.runkids.cc/docs/understand/agents)。



## 另請參閱



- [啟用/停用](https://skillshare.runkids.cc/docs/reference/commands/enable) — 切換技能而不刪除
- [安裝](https://skillshare.runkids.cc/docs/reference/commands/install) — 安裝技巧
- [卸載](https://skillshare.runkids.cc/docs/reference/commands/uninstall) — 刪除技能
- [狀態](https://skillshare.runkids.cc/docs/reference/commands/status) — 顯示同步狀態
- [代理](https://skillshare.runkids.cc/docs/understand/agents) — 代理概念