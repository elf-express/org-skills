# Commands

> Source: https://skillshare.runkids.cc/docs/reference/commands/

---

# 指令



所有技能共享指令的完整參考。



## 你想做什麼？號



|我想... |命令|
| ---| ---|
|首次設定技能共享 |初始化|
|從 GitHub 安裝技能 |安裝 |
|創造我自己的技能|新 |
|將技能同步到所有 AI CLI |同步|
|檢查哪些內容不同步 |狀態/差異|
|搜尋社區技能 |搜尋 |
|更新已安裝技能 |檢查然後更新|
|暫時隱藏技能而不刪除它 |啟用/停用 |
|使用 git 儲存或同步變更 |提交/推/拉 |
|管理非技能資源（規則、指令） |額外 |
|管理單檔 .md 代理程式 |大多數指令接受代理程式或 --kind agent — 請參閱代理程式 |
|查看哪些技能使用最多的上下文標記 |分析|
|修復損壞的東西 |醫生|
|在我的 shell 中啟用製表符補全 |完成 |
|開啟網頁儀表板 |使用者介面 |



---



## 概述



|類別 |命令 |
| ---| ---|
|核心|初始化、安裝、解除安裝、清單、搜尋、同步、狀態 |
|技能管理|新建、檢查、更新、升級、啟用、停用 |
|目標管理|目標，差異|
|額外管理|額外（初始化、清單、刪除、收集）|
|同步作業|收集、備份、還原、垃圾、提交、推、拉 |
|安全與公用事業|分析、審計、集線器、日誌、醫生、tui、ui、完成、版本 |



---



## 核心指令



|命令|描述 |
| ---| ---|
|初始化|首次设置 |
|安装 |从存储库或路径添加技能 |
|解除安裝 |刪除技能 |
|列表 |列出所有技能 |
|搜尋 |搜尋技能 |
|同步|将技能推向所有目标|
|狀態 |顯示同步狀態 |



## 技能管理



|命令|描述 |
| ---| ---|
|新 |創造新技能 |
|檢查 |檢查可用更新 |
|更新 |更新技能或追蹤的儲存庫 |
|升級|升級 CLI 或內建技能 |
|啟用/停用 |暫時啟用或停用技能 |



## 目標管理



|命令|描述 |
| ---| ---|
|目標|管理目標 |
|差異|顯示來源與目標之間的差異 |



## 額外管理



|命令|描述 |
| ---| ---|
|額外 |管理非技能資源（規則、指令、提示）|



## 同步操作



|命令|描述 |
| ---| ---|
|收集|從目標到來源收集技能 |
|備份|建立目標的備份 |
|還原 |從備份還原目標 |
|垃圾|管理垃圾桶中已卸載的技能 |
|提交 |建立本地 git 提交而不推送 |
|推|提交並推送到 git remote |
|拉|從 git 遠端拉取並同步 |



## 安全與公用事業



|命令|描述 |
| ---| ---|
|分析|分析上下文視窗使用 |
|稽核|安全威脅掃描技巧|
|日誌 |查看操作和審核日誌 |
|醫生|診斷問題 |
|推|切換互動式 TUI 模式 |
|使用者介面 |啟動網路儀表板 |
|樞紐|管理技能中心來源 |
|完成 |產生 shell 完成腳本 |
|版本 |顯示 CLI 版本 |



---



## 常用標誌



大多數命令支援：



|旗幟|描述 |
| ---| ---|
| --dry-run，-n |預覽而不進行更改 |
| --幫助，-h |顯示幫助 |



---



## 快速參考



```
# Setup
skillshare init
skillshare init --remote git@github.com:you/skills.git
# Install skills
skillshare install anthropics/skills/skills/pdf
skillshare install github.com/team/skills --track
# Create skill
skillshare new my-skill
# Sync
skillshare sync
skillshare sync --dry-run
# Git checkpoints / cross-machine
skillshare commit -m "Update skill"
skillshare push -m "Add skill"
skillshare pull
# Status
skillshare status
skillshare list
skillshare diff
# Enable/disable skills
skillshare disable draft-*
skillshare enable draft-*
# Maintenance
skillshare update --all
skillshare analyze
skillshare audit
skillshare log
skillshare doctor
skillshare backup
# TUI preferences
skillshare tui            # Show current status
skillshare tui off        # Disable interactive TUI
skillshare tui on         # Re-enable TUI
# Web UI
skillshare ui
skillshare ui -p          # Project mode
# Hub
skillshare hub list
skillshare hub add https://hub.example.com/index.json
# Check for updates
skillshare check
# Trash management
skillshare trash list
skillshare trash restore my-skill
# Shell completion
skillshare completion bash --install
skillshare completion zsh --install
# Version
skillshare version
```



---



## 相關



- [快速參考](https://skillshare.runkids.cc/docs/getting-started/quick-reference) — 指令備忘單
- [工作流程](https://skillshare.runkids.cc/docs/how-to/daily-tasks) — 常見使用模式