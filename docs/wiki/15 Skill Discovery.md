# Skill Discovery

> Source: https://skillshare.runkids.cc/docs/how-to/daily-tasks/skill-discovery

---

# 技能發現



從社區中尋找、評估和安裝技能。



## 概述



搜尋

瀏覽

評價

安裝

同步



---



## 第 1 步：搜尋



按關鍵字尋找技能，或瀏覽熱門技能：



```
skillshare search              # Browse popular skills
skillshare search pdf
skillshare search "code review"
skillshare search react
```



---



## 第 2 步：瀏覽儲存庫



探索儲存庫中的技能：



```
# Official Anthropic skills
skillshare install anthropics/skills
# Community skills
skillshare install ComposioHQ/awesome-claude-skills
```



這將進入 **發現模式** — 顯示儲存庫中的所有可用技能。



---



## 第 3 步：評估



安裝前，請考慮：



- **它能解決我的問題嗎？ ** 閱讀說明
- **維護良好嗎？ ** 檢查儲存庫的活動
- **會衝突嗎？ ** 檢查與現有技能的名稱衝突



預覽將安裝的內容：



```
skillshare install anthropics/skills/skills/pdf --dry-run
```



---



## 第 4 步：安裝



### 單一技能



```
skillshare install anthropics/skills/skills/pdf
```



### 一個儲存庫中的多種技能



```
# Interactive browse
skillshare install anthropics/skills
# Select specific skills (non-interactive)
skillshare install anthropics/skills -s pdf,commit
# Install all skills
skillshare install anthropics/skills --all
```



### 整個儲存庫（針對團隊）



```
skillshare install github.com/team/skills --track
```



---



## 步驟 5：同步



安裝後不要忘記同步：



```
skillshare sync
```



---



## 熱門技能來源



|來源 |網址 |
| ---| ---|
|人擇官方|人類學/技能 |
| Vercel 代理技能 | vercel-labs/代理技能 |
|社群 |技能網 |



---



## 發現指令



|命令|目的|
| ---| ---|
|搜尋 |瀏覽熱門技能 |
|搜尋 <查詢> |搜尋技能 |
|檢查|檢查可用更新 |
|安裝 <repo> |瀏覽儲存庫（發現模式）|
|安裝 <repo/path> |安裝具體技巧|
|清單 |顯示裝機技巧 |



---



## 安裝選項



```
# Custom name
skillshare install anthropics/skills/skills/pdf --name my-pdf
# Force overwrite
skillshare install anthropics/skills/skills/pdf --force
# Update existing
skillshare install anthropics/skills/skills/pdf --update
# Track for team sharing
skillshare install github.com/team/skills --track
```



`--name`僅當安裝目標為單一技能時有效。  
 將 `--name` 與傳回多個技能的儲存庫發現一起使用將傳回錯誤。



---



## 安裝後



### 驗證



```
skillshare list
skillshare status
```



### 測試



使用 AI CLI 中的技能確保其按預期工作。



### 檢查更新



```
skillshare check              # See what has updates available
```



### 稍後更新



```
# Single skill (with source metadata)
skillshare install pdf --update
# Tracked repo
skillshare update _team-skills
```



---



## 另請參閱



- [搜尋](https://skillshare.runkids.cc/docs/reference/commands/search) — 搜尋指令參考
- [install](https://skillshare.runkids.cc/docs/reference/commands/install) — 安裝指令參考
- [中心索引](https://skillshare.runkids.cc/docs/how-to/sharing/hub-index) — 管理技能中心
- [日常工作流程](https://skillshare.runkids.cc/docs/how-to/daily-tasks/daily-workflow) — 安裝後，日常使用