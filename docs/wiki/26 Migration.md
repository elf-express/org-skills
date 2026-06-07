# Migration

> Source: https://skillshare.runkids.cc/docs/how-to/advanced/migration

---

# 遷移



從其他技能管理方法遷移到技能共享。



## 來自手動管理



如果您一直在 AI CLI 之間手動複製技能：



### 第 1 步：初始化技能共享



```
skillshare init
```



### 第 2 步：收集現有技能



```
# Collect from each AI CLI
skillshare collect claude
skillshare collect cursor
skillshare collect codex
# Or collect from all at once
skillshare collect --all
```



### 第 3 步：處理重複項



如果同一技能存在於多個地方，`collect`會發出警告。選擇保留哪一個。



### 第 4 步：同步



```
skillshare sync
```



現在所有目標都符號連結到您的單一來源。



---



## 來自其他安裝工具



如果您使用過 `npx install-skill` 或類似的：



### 第 1 步：初始化技能共享



```
skillshare init
```



### 第 2 步：備份現有技能



```
skillshare backup
```



### 第 3 步：收集或重新安裝



**選項 A：收集現有**（保留目前版本）



```
skillshare collect --all
```



**選項 B：從來源重新安裝**（取得最新版本）



```
# Check the metadata
cat ~/.config/skillshare/skills/.metadata.json
# Reinstall
skillshare install anthropics/skills/skills/pdf
```



### 第 4 步：同步



```
skillshare sync
```



---



## 來自 Git 子模組



如果您一直在使用 git 子模組：



### 第 1 步：匯出子模組內容



```
# In your existing skills repo
git submodule foreach 'cp -r $toplevel/$sm_path ~/temp-skills/$name'
```



### 第 2 步：初始化技能共享



```
skillshare init
```



### 第 3 步：導入技能



```
# Copy to source
cp -r ~/temp-skills/* ~/.config/skillshare/skills/
# Or install as tracked repos
skillshare install github.com/org/skill-repo --track
```



### 第 4 步：同步



```
skillshare sync
```



---



## 來自承諾的專案技能



如果您的儲存庫已在 `.claude/skills/`、`.cursor/skills/` 或類似目錄中提交了技能：



### 第 1 步：初始化專案模式



```
cd my-project
skillshare init -p
```



### 第2步：將技能移至`.skillshare/skills/`



```
# Copy existing skills to skillshare source
cp -r .claude/skills/my-skill .skillshare/skills/
cp -r .claude/skills/api-guide .skillshare/skills/
# Remove originals (sync will recreate as symlinks)
rm -rf .claude/skills/my-skill .claude/skills/api-guide
```



### 第 3 步：同步



```
skillshare sync
```



現在`.claude/skills/my-skill`是`.skillshare/skills/my-skill`的符號連結－所有其他目標（遊標、風帆衝浪等）都會自動獲得相同的技能。



### 第 4 步：提交遷移



```
git add .skillshare/ .claude/skills/ .cursor/skills/
git commit -m "Migrate project skills to skillshare"
```

多工具優勢

之前：技能只能在一個 AI CLI 中發揮作用。之後：相同的技能在每個配置的目標中自動可用。



---



## 來自團隊特定的解決方案



如果您的團隊有自訂技能共享：



### 第 1 步：確定目前方法



- 技能儲存在哪裡？
- 它們是如何共享的？
- 它們如何更新？



### 第 2 步：選擇您的遷移路徑



**選項 A：全域模式** — 每台電腦上的所有項目都可用的技能。



```
# Create team skills repo
cp -r /current/team/skills ~/new-team-skills
cd ~/new-team-skills && git init && git add . && git commit -m "Migrate to skillshare"
git push origin main
# Team members install globally
skillshare install github.com/org/team-skills --track && skillshare sync
```



**選項 B：專案模式** — 技能範圍僅限於特定儲存庫，透過 git 共用。



```
cd my-project
skillshare init -p
# Move team skills into project source
cp -r /current/team/skills/* .skillshare/skills/
# Sync and commit
skillshare sync
git add .skillshare/
git commit -m "Add team skills via skillshare"
```



新團隊成員獲得一切：



```
git clone github.com/org/my-project
cd my-project
skillshare install -p && skillshare sync
```



**選項 C：兩者** — 全球組織範圍標準、每個儲存庫的專案特定技能。



```
# Organization standards (global)
skillshare install github.com/org/standards --track && skillshare sync
# Project-specific skills (project mode)
cd my-project
skillshare init -p
skillshare install github.com/org/project-skills -p && skillshare sync
```

選擇哪個？

- **全球**：編碼標準、安全審計－每個專案都需要的東西
- **專案**：API 約定、網域規則、部署指南 - 特定於一個儲存庫的內容
- **兩者**：大多數團隊隨著成長而最終到達這裡



---



## 從全域到項目



如果您擁有屬於特定項目的全域模式技能：



### 第 1 步：初始化專案模式



```
cd my-project
skillshare init -p
```



### 第 2 步：從全球來源複製技能



```
# Copy specific skills
cp -r ~/.config/skillshare/skills/api-guide .skillshare/skills/
cp -r ~/.config/skillshare/skills/deploy-rules .skillshare/skills/
```



### 步驟 3：從全域中刪除（可選）



```
skillshare uninstall api-guide
skillshare uninstall deploy-rules
skillshare sync   # Clean up global symlinks
```



### 第 4 步：同步並提交



```
skillshare sync   # Auto-detects project mode
git add .skillshare/
git commit -m "Move project-specific skills to project mode"
```



此後，技能的範圍僅限於此儲存庫，並透過 git 與團隊共享 — 不再使您的全域設定變得混亂。



---



## 保存歷史



如果你想保留 git 歷史記錄：



### 對於個人技能



```
# Clone your existing repo to skillshare location
git clone your-existing-repo ~/.config/skillshare/skills
# Initialize skillshare with existing source
skillshare init --source ~/.config/skillshare/skills
```



### 對於團隊儲存庫



```
# Use --track to preserve .git
skillshare install github.com/team/skills --track
```



---



## 回滾



如果遷移出錯：



### 從備份還原



```
skillshare restore claude
skillshare restore cursor
```



### 重新開始



```
rm ~/.config/skillshare/config.yaml
skillshare init
```



---



## 清單



遷移前：



- 列出所有目前技能位置
- 識別重複項
- 記下任何自訂配置
- 建立備份



遷移後：



- 驗證`skillshare list`中出現的所有技能
- 在每個 AI CLI 中測試技能
- 設定 git 遠端（如果需要）
- 與團隊分享新的工作流程



---



## 另請參閱



- [來自現有技能](https://skillshare.runkids.cc/docs/getting-started/from-existing-skills) — 快速遷移路徑
- [收集](https://skillshare.runkids.cc/docs/reference/commands/collect) — 從目標收集
- [比較](https://skillshare.runkids.cc/docs/understand/philosophy/comparison) — 比較法