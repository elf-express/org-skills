# Project Workflow

> Source: https://skillshare.runkids.cc/docs/how-to/daily-tasks/project-workflow

---

# 專案工作流程



專案級技能管理的編輯→同步→提交週期。



## 概述



編輯

同步

犯罪

推

.skillshare/技能/

.claude/.cursor/等

偏僻的

團隊



---



## 團隊協作場景



典型的團隊工作流程顯示專案技能如何保持同步：



```
Alice (adds a skill)                    Bob (gets the update)──────────────────────                  ──────────────────────
skillshare new api-guide -p
$EDITOR .skillshare/skills/api-guide/
skillshare sync
git add . && git commit && git push
                                        git pull
                                        skillshare install -p
                                        skillshare sync
                                        → api-guide now in .claude/skills/
```



Bob 不需要知道增加了哪些技能 - `skillshare install -p` 讀取配置並安裝列出的所有內容。



---



## 常用操作



### 新增技能



```
# Create the skill
skillshare new my-skill -p
$EDITOR .skillshare/skills/my-skill/SKILL.md
# Sync to targets
skillshare sync
# Commit
git add .skillshare/
git commit -m "Add my-skill"
```



### 新增代理



代理程式為單一`.md`檔案；直接在`.skillshare/agents/`下建立它們：



```
# Create an agent file
$EDITOR .skillshare/agents/my-agent.md
# Sync to agent-capable targets (claude, cursor, augment, opencode)
skillshare sync agents
# Commit
git add .skillshare/agents/
git commit -m "Add my-agent"
```



`skillshare sync`（沒有`agents`）一次性同步技能和代理。使用`skillshare disable my-agent --kind agent -p`為`.skillshare/agents/.agentignore`新增條目而不刪除檔案。



### 安裝遠端技能



```
# Install from GitHub
skillshare install anthropics/skills/skills/pdf -p
# Sync to targets
skillshare sync
# Commit config changes
git add .skillshare/
git commit -m "Add pdf skill from anthropic"
```



### 更新遠端技能



```
# Update a specific skill
skillshare update pdf -p
# Or update all remote skills
skillshare update --all -p
# Sync updated skills
skillshare sync
# Commit if config changed
git add .skillshare/
git commit -m "Update remote skills"
```



### 刪除技能



```
# Uninstall
skillshare uninstall my-skill -p
# Sync to clean up symlinks
skillshare sync
# Commit
git add .skillshare/
git commit -m "Remove my-skill"
```



### 任何人都可以加入該專案



無論是新團隊成員、開源貢獻者還是嘗試社群範本的人 - 設定都是相同的：



```
# Clone the project
git clone github.com/team/project
cd project
# Install remote skills listed in config
skillshare install -p
# Sync to targets
skillshare sync
```



`config.yaml` 充當便攜式技能清單 - 無需手動尋找技能。



---



## 管理目標



### 新增目標



```
# Add a known target
skillshare target add windsurf -p
# Add a custom target with path
skillshare target add custom-tool ./tools/ai/skills -p
# Sync to new target
skillshare sync
```



### 刪除目標



```
skillshare target remove windsurf -p
```



### 列出目標



```
skillshare target list -p
```



```
Project Targets  claude    .claude/skills (merge)  cursor         .cursor/skills (merge)
```



---



## 檢查狀態



```
skillshare status
```



```
Project Skills (.skillshare/)Source  ✓ .skillshare/skills (3 skills)Targets  ✓ claude       [merge] .claude/skills (3 synced)  ✓ cursor       [merge] .cursor/skills (3 synced)Remote Skills  ✓ pdf          anthropic/skills/pdf  ✓ review       github.com/team/tools
```



---



## 列出技能



```
skillshare list
```



```
Installed skills (project)─────────────────────────────────────────  → my-skill            local  → pdf                 anthropic/skills/pdf  → review              github.com/team/tools→ 3 skill(s): 2 remote, 1 local
```



---



## 網路儀表板



使用 Web UI 進行視覺化專案技能管理：



```
skillshare ui -p
```



或如果 `.skillshare/config.yaml` 存在（自動偵測到），則僅使用 `skillshare ui`。儀表板隱藏Git Sync（使用您專案自己的git）並直接編輯`.skillshare/config.yaml`。



---



## 提示



### 自動偵測



一旦`.skillshare/config.yaml`存在，大多數指令都會自動偵測項目模式：



```
cd my-project/
skillshare sync          # Auto project mode
skillshare status        # Auto project mode
skillshare list          # Auto project mode
```

零配置

只需將 `cd` 放入專案目錄即可 — Skillshare 偵測到 `.skillshare/config.yaml` 並自動切換到專案模式。不需要標誌。



### 立即編輯並查看變更



技能是符號連結的 - 在 `.skillshare/skills/` 中進行的編輯在目標中立即可見：



```
$EDITOR .skillshare/skills/my-skill/SKILL.md
# Change is already in .claude/skills/my-skill/ (symlink)
```



僅在新增/刪除技能或目標時運行`sync`。



### 同步前預覽



```
skillshare sync --dry-run
```



---



## 另請參閱



- [專案技巧](https://skillshare.runkids.cc/docs/understand/project-skills) — 概念解釋
- [專案設定](https://skillshare.runkids.cc/docs/how-to/sharing/project-setup) — 初始設定指南
- [日常工作流程](https://skillshare.runkids.cc/docs/how-to/daily-tasks/daily-workflow) — 全域模式日常使用