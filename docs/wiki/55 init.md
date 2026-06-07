# init

> Source: https://skillshare.runkids.cc/docs/reference/commands/init

---

＃ 熱



首次設定。自動偵測已安裝的 AI CLI 並配置目標。



```
skillshare init              # Interactive setup
skillshare init --dry-run    # Preview without changes
```



## 何時使用



- 第一次在機器上設定技能共享
- 遷移到新電腦（使用`--remote`連接到現有儲存庫）
- 將技能共享新增至專案（使用`--project`）
- 發現新安裝的AI CLI（使用`--discover`）



## 發生了什麼事



技能共享初始化

0. 來源路徑提示1.建立來源+代理目錄2。自动检测 AI CLI3。初始化git4。設定遠端5。子目錄提示6.建立config.yaml7。內建技能

`init` 一步建立技能來源目錄**和** `agents/` 同級目錄，因此兩種資源類型都可以立即使用。代理目錄是靜默的－沒有額外的提示或標誌。代理文件格式請參閱[代理](https://skillshare.runkids.cc/docs/understand/agents)。

通用目標

當偵測到任何 AI CLI 時，`init` 自動推薦 **通用** 目標 (`~/.agents/skills`)。這是 [vercel-labs/skills](https://github.com/vercel-labs/skills) (`npx skills list`) 使用的共享目錄，用於一次向所有相容代理提供技能。

代理源路徑

代理源預設為`<source parent>/agents`（因此預設安裝為`~/.config/skillshare/agents/`）。在 `config.yaml` 中設定 `agents_source:` 以覆蓋該位置。專案模式始終使用 `.skillshare/agents/` 且不支援 `agents_source`。支援代理的目標（Claude、Cursor、Augment、OpenCode）在運行 `skillshare sync` 後會自動選擇代理。



## 專案模式



使用`-p`初始化專案級技能：



```
skillshare init -p                              # Interactive
skillshare init -p --targets claude,cursor  # Non-interactive
```



### 發生了什麼



技能共享初始化-p

1. 創建.skillshare/skills + .skillshare/agents2。檢測 AI CLI 目錄3。建立目標技能目錄4。編寫config.yaml

初始化後，將`.skillshare/`提交到git（`skills/`和`agents/`）。完整指南請參閱[專案設定](https://skillshare.runkids.cc/docs/how-to/sharing/project-setup)。



## 發現模式



在現有設定上重新執行 init 以偵測並新增新的 AI CLI 目標：



### 全球



```
skillshare init --discover              # Interactive selection
skillshare init --discover --select codex,opencode  # Non-interactive
```



掃描您的配置中尚未安裝的新安裝的 AI CLI，並提示您新增它們。每當偵測到任何 CLI 時，都會自動建議 `universal` 目標 (`~/.agents/skills`)。



### 項目



```
skillshare init -p --discover           # Interactive selection
skillshare init -p --discover --select antigravity  # Non-interactive
```



掃描專案目錄以尋找新的 AI CLI 目錄（例如，`.agents/`）並將其新增為目標。



### 發現+模式行為



當您將 `--discover` 與 `--mode` 組合時，該模式**僅**套用於此發現運行中新增的目標。配置中的現有目標保持不變。



```
# Adds cursor with mode=copy, does not change existing targets
skillshare init --discover --select cursor --mode copy
# Project mode variant (same rule)
skillshare init -p --discover --select cursor --mode copy
```

提示

如果您在沒有 `--discover` 的情況下在已初始化的設定上執行 `skillshare init`，錯誤訊息將提示您使用它。



## 選項



|旗幟|描述 |
| ---| ---|
| --source, -s <路徑> |自訂原始碼目錄（不設定則互動模式提示）|
| --remote <url> |設定git遠端（暗示--git；如果遠端有技能則自動拉取；遠端有技能時跳過內建技能提示）|
| --項目，-p |在目前目錄中初始化項目級技能 |
| --copy-from, -c <名稱\|路徑> |從特定 CLI 或路徑複製技能 |
| --無複製|從空源開始（跳過複製提示）|
| --targets, -t <列表> |逗號分隔的目標名稱 |
| --所有目標 |新增所有偵測到的目標 |
| --無目標 |跳過目標選擇 |
| --模式，-m <模式> |為新配置的目標設定預設模式（合併、複製、符號連結）。使用 --discover 時，僅影響新建的目標。 |
| --git |初始化git而不提示|
| --沒有-git |跳過 git 初始化 |
| --技能|安裝內建技能分享技能而不提示（將 /skillshare 新增至 AI CLI） |
| --無技能|跳過內建技能安裝 |
| --發現，-d |偵測新的 AI CLI 目標並將其新增至現有設定 |
| --選擇 <列表> |要新增的以逗號分隔的目標（需要 --discover）|
| --配置本機| Gitignore config.yaml，以便每位開發人員管理自己的目標（僅限專案模式）。請參閱集中技能儲存庫配方。 |
| --git-root <範圍> |用於提交/推/拉操作的目錄（預設技能、代理、附加、根）。 root 版本的技能 + 代理 + 附加功能集中在一個儲存庫中，並自動忽略 config.yaml。還在設定過程中以互動方式提供。稍後重新運行 Skillshare init --git-root <scope> 以無頭切換範圍 - 它會在新範圍內初始化存儲庫並保留設置，但不會移動現有歷史記錄。 |
| --subdir <姓名> |使用子目錄作為來源路徑（例如技能）|
| --dry-run，-n |預覽無變更 |



`init` 設定您的啟動模式策略。您以後可以隨時對每個目標進行微調：



```
skillshare target cursor --mode copy
skillshare sync
```



## 來源子目錄



預設情況下，`init --remote`將整個git repo根視為技能來源。如果您的儲存庫還包含非技能檔案（README、CI 設定、點檔案等），您可以將技能儲存在子目錄中：



```
# Without --subdir: repo root = source (all files are skills)
~/.config/skillshare/skills/          ← git repo root = source  ├── my-skill/  └── another-skill/
# With --subdir skills: source points to a subdirectory
~/.config/skillshare/skills/          ← git repo root  ├── README.md  ├── .github/  └── skills/                         ← source points here      ├── my-skill/      └── another-skill/
```



典型用例：將技能嵌入現有的點文件或 monorepo，而不是專用的僅限技能的儲存庫。



```
# Interactive: prompts during init
skillshare init --remote git@github.com:you/dotfiles.git
# Non-interactive: specify directly
skillshare init --remote git@github.com:you/dotfiles.git --subdir skills
```



## 常見場景



### 遠端設定（選擇一項）



互動式（當您需要引導提示時建議首次設定）：



```
skillshare init --remote git@github.com:you/my-skills.git
```



非互動式（無提示，自動偵測已安裝的目標）：



```
skillshare init --remote git@github.com:you/my-skills.git --no-copy --all-targets --no-skill
```



非互動式（無提示，立即匯入現有的克勞德技能）：



```
skillshare init --remote git@github.com:you/my-skills.git --copy-from claude --all-targets --no-skill
```



### 集中技能儲存庫



```
# Creator: set up shared repo with local config
skillshare init -p --config local --targets claude
# Teammate: clone and auto-detect shared repo
git clone <repo> && cd <repo>
skillshare init -p
skillshare target add myproject ~/DEV/myproject/.claude/skills -p
```



### 其他場景



```
# Standard setup (auto-detect everything)
skillshare init
# Use existing skills directory
skillshare init --source ~/.config/skillshare/skills
# Project-level setup
skillshare init -p
skillshare init -p --targets claude,cursor
# Fully non-interactive setup
skillshare init --no-copy --all-targets --git --skill
# Start with copy mode defaults for newly added targets
skillshare init --mode copy
# Add newly installed CLIs to existing config
skillshare init --discover
skillshare init -p --discover
# Add a newly discovered target and force copy mode only for that new target
skillshare init --discover --select cursor --mode copy
```