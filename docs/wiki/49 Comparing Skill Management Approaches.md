# Comparing Skill Management Approaches

> Source: https://skillshare.runkids.cc/docs/understand/philosophy/comparison

---

# 比較技能管理方法



本頁面比較了 AI CLI 技能管理的兩種主要架構方法：**命令式**（按命令安裝）和 **聲明式**（設定 + 同步）。



如果您正在評估工具或考慮轉換，此細分將幫助您了解基本的設計差異。



## 架構概覽



### 命令式（按命令安裝）



命令式工具使用按命令安裝的模型 - 每個安裝都是一個獨立的操作：



```
tool add owner/repo → select agents → choose method → donetool add owner/repo → select agents → choose method → donetool add owner/repo → select agents → choose method → done
```



每個操作都需要使用者輸入。沒有描述「什麼應該安裝在哪裡」的持久狀態。



### 聲明式（配置 + 同步）



Skillshare 使用宣告模型 - 您定義一次所需的狀態，然後同步：



```
# config.yaml — define oncesource: ~/.config/skillshare/skillstargets:  claude: ~/.claude  cursor: ~/.cursor/skills  codex: ~/.codex/skills
```



```
skillshare sync  # reconcile actual state to desired state
```



一個指令，無提示，每次都能得到確定的結果。



## 功能比較



|能力|命令式（依指令安裝）|聲明式（技能分享）|
| --- | --- | --- |
|設定|沒有設定檔；每次執行都會提示| config.yaml — 設定一次，永遠重複使用 |
|代理程式選擇|每次互動提示|在配置中定義；同步處理所有 |
|安裝方法|每個操作選擇複製/符號連結 |配置中的sync_mode（合併、複製或符號連結）|
|單一事實來源 |技能獨立複製給每個特工 |來源目錄→所有目標的符號連結|
|刪除一名特工的一項技能 |可能會刪除源文件，破壞其他代理|只影響該目標的符號鏈接 |
|可重複的設定 |沒有內建的方法可以在新機器上恢復| config.yaml + 來源目錄 = 完全恢復 |
|專案範圍技能 |鎖定檔案僅追蹤全域 | Skillshare init -p 用於每個儲存庫技能 |
|跨機同步 |手動（透過點檔案同步鎖定檔）|使用 git 內建推/拉 |
|雙向流動|單向（僅安裝）|收集從目標中拉回改進|
|區分自己的技能和安裝的技能|混合在同一目錄|追蹤的倉庫使用 _ 前綴 |
|離線操作| CLI 本身需要 npx + 網路 |單一二進位文件，安裝後離線工作 |
|網頁儀表板 |無 | Skillshare ui — 視覺化管理 |
|備份/還原|無 |技能共享備份/技能共享恢復|
| Git 平台支援 | GitHub 僅用於更新/檢查（硬編碼到 GitHub Trees API）|任何 Git 遠端 — GitHub、GitLab、Bitbucket、Azure DevOps、Gitea、AtomGit、Gitee、自架 |
|執行時期依賴 | Node.js + npm |無（單一 Go 二進位）|



## 解決常見痛點



###「我每次安裝時都必須選擇代理」



使用技能共享，您只需配置一次目標：



```
targets:  claude: ~/.claude  cursor: ~/.cursor/skills
```



然後每個`sync`、`install`或`collect`都知道要去哪裡。沒有提示。



### “從一個特工身上刪除一項技能會破壞其他特工的技能”



在命令式工具中，從一個代理中刪除一項技能可能會刪除共享來源文件，從而給其他代理留下損壞的符號連結。



Skillshare 的架構完全阻止了這種情況——來源目錄是唯一的事實。目標符號連結指向**到**來源。刪除目標只會刪除該目標的符號連結；來源檔案未受影響。



```
Source: ~/.config/skillshare/skills/my-skill/SKILL.md  (always preserved)  ├── ~/.claude/skills/my-skill → symlink to source  ✓  ├── ~/.cursor/skills/my-skill  → symlink to source  ✓  (unaffected)  └── ~/.codex/skills/my-skill  → symlink to source  ✓  (unaffected)
```



### “我無法在新機器上恢復我的設定”



借助 Skillshare，您的整個設定都是可移植的：



1.版本控制`~/.config/skillshare/`（來源+配置）
2. 在新機器上：`git clone`你的設定倉庫
3.運行`skillshare sync`



所有目標都會立即重新建立。



### “更新和檢查不適用於 GitLab / Bitbucket / Azure DevOps”



命令式工具通常依賴 GitHub Trees API 進行更新檢查，這意味著 `update` 和 `check` 會默默地跳過非 GitHub 來源的技能。



Skillshare 使用**本地 git 操作**（`git fetch` + 樹哈希比較）——它適用於任何 Git 遠程，包括 GitLab、Bitbucket、Azure DevOps、Gitea、AtomGit、Gitee 和任何自託管實例。不需要特定於平台的 API。



```
# All of these support install, update, and check:
skillshare install https://gitlab.com/team/skills
skillshare install git@bitbucket.org:company/private-skills.git
skillshare install https://git.mycompany.com/org/repo
skillshare update   # checks all sources, regardless of host
```



### “在大型儲存庫上複製需要很長時間”



Skillshare 預設使用淺克隆 (`--depth 1`) 進行非追蹤安裝，從而顯著縮短下載時間。對於需要完整歷史記錄的追蹤儲存庫，請使用 `--track`。



### “我的技能分散在代理商目錄中”



Skillshare 將所有內容都集中在一個地方：



```
~/.config/skillshare/skills/├── my-custom-skill/          # Your own skills├── react-best-practices/     # Installed skills├── _team-repo/               # Tracked repos (prefixed with _)│   ├── frontend-guidelines/│   └── code-review/└── _another-org-repo/
```



`_` 前綴清楚地將追蹤的（團隊/組織）儲存庫與您的個人技能分開。



## 遷移到技能共享



如果您已經在使用其他技能管理器：



### 第 1 步：安裝 Skillshare



```
# macOS / Linux
curl -fsSL https://raw.githubusercontent.com/runkids/skillshare/main/install.sh | sh
# Homebrew
brew install skillshare
```



### 第 2 步：初始化並收集現有技能



```
skillshare init              # Creates config and detects targets
skillshare collect --all     # Imports existing skills from all detected targets
```



### 第 3 步：同步



```
skillshare sync              # Symlinks source skills to all targets
```



您現有的技能現在可以從一個地方進行管理。詳細演練請參考【遷移指南】(https://skillshare.runkids.cc/docs/how-to/advanced/migration)。



## 選擇正確的工具



**選擇命令式工具，如果：**



- 你很少安裝技能且不介意互動式提示
- 您只使用一個 AI CLI
- 您不需要跨機器或團隊工作流程



**如果符合以下條件，請選擇技能共享：**



- 您使用多個 AI CLI 並希望它們同步
- 您想要一勞永逸的配置
- 您在多台機器上工作
- 您與團隊或組織分享技能
- 您需要備份、復原和版本控制來發揮您的技能
- 您在 GitLab、Bitbucket、Azure DevOps 或自架 Git 上託管技能
- 您喜歡沒有運行時依賴的單一二進位文件
- 您不希望在本機工作流程之外追蹤安裝/下載活動



---



## 另請參閱



- [遷移](https://skillshare.runkids.cc/docs/how-to/advanced/migration) — 遷移指南
- [核心概念](https://skillshare.runkids.cc/docs/understand) — 技能分享如何運作