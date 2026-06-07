# Declarative Skill Manifest

> Source: https://skillshare.runkids.cc/docs/understand/declarative-manifest

---

# 聲明性技能清單



將您的技能集合定義為程式碼 - 從單一清單檔案安裝、共用和重現設定。

這什麼時候重要？

當您希望跨機器進行可重複的技能設定、使用單一指令進行團隊訓練或開源專案引導時，請使用聲明性清單。



## 什麼是技能清單？號



技能清單是您的技能集合的**便攜式聲明**。您無需一一手動安裝技能，而是將它們列在清單檔案中並執行 `skillshare install` 來顯示所有內容。



清單位置取決於模式：



|模式|清單位置 |可承諾嗎？ |
| --- | --- | --- |
|項目| .skillshare/config.yaml（技能：部分）|是的－承諾與團隊分享 |
|全球| 〜/.config/skillshare/skills/.metadata.json |否 — 個人機器狀態 |



### 專案模式清單



在專案模式下，`skills:` 與 `targets:` 一起存在於 `config.yaml` 中：



```
# .skillshare/config.yamltargets:  - claude  - cursorskills:  - name: react-best-practices    source: anthropics/skills/skills/react-best-practices    group: frontend  - name: _team-skills    source: my-org/shared-skills    tracked: true  - name: commit    source: anthropics/skills/skills/commit
```



該檔案已提交至 git — 隊友複製儲存庫並執行 `skillshare install -p` 安裝所有列出的技能。



### 全域模式清單



在全域模式下，技能記錄儲存在`.metadata.json`（集中元資料儲存）中。該檔案還包含運行時追蹤資料（哈希值、時間戳記）並且是自動管理的。



## 工作原理



### 從清單安裝



在**不帶參數**的情況下運行 `skillshare install` 會讀取清單並安裝所有列出的技能：



```
# Global mode — installs all skills from ~/.config/skillshare/skills/.metadata.json
skillshare install
# Project mode — installs all skills from .skillshare/config.yaml skills: section
skillshare install -p
# Preview without installing
skillshare install --dry-run
```



已經存在的技能將自動跳過。



### 自動對帳



清單與您的實際技能集合保持同步：



- **`skillshare install <source>`** — 自動將已安裝的技能加入清單中
- **`skillshare uninstall <name>...`** — 自動從清單中刪除條目



在專案模式下，`config.yaml` 會更新。在全域模式下，`.metadata.json` 會更新。您永遠不需要手動編輯清單（儘管可以）。



## 技能輸入欄位



`skills:` 清單中的每個條目都有以下欄位：



|領域 |必填 |說明 |
| --- | --- | --- |
|名稱 |是的 | Skill name (directory name in source) |
|來源 |是的 |安裝來源（GitHub 簡寫、HTTPS URL、SSH URL）|
|追蹤 |沒有 | true for tracked repositories (preserves .git) |
|組 |沒有 |子目錄路徑（例如 frontend 或 frontend/vue）。 Corresponds to --into during install. |



## 使用案例



### 個人設定



跨機器維護您的個人技能集合：



```
# On machine A — skills are already installed and tracked in registry
skillshare push   # backup config + registry to git
# On machine B — fresh machine
skillshare pull   # restore config + registry from git
skillshare install  # install all skills from manifest
skillshare sync   # distribute to all targets
```



### 團隊入職



新團隊成員透過一個指令即可獲得相同的 AI 上下文：



```
# .skillshare/config.yaml skills: section is committed to the repo
git clone <project-repo>
cd <project-repo>
skillshare install -p   # installs all declared skills
skillshare sync -p      # links to project targets
```



### 開源引導程式



專案維護者在`config.yaml`中聲明推薦技能：



```
# .skillshare/config.yamltargets:  - claude  - cursorskills:  - name: react-best-practices    source: anthropics/skills/skills/react-best-practices    group: frontend  - name: commit    source: anthropics/skills/skills/commit
```

組場和`--into`

當您使用`--into`安裝時，會自動記錄群組：

```
skillshare install anthropics/skills/skills/pdf --into frontend -p
# config.yaml will contain: name: pdf, group: frontend
```

運行 `skillshare install -p` （無參數）會從清單中重新建立相同的目錄結構。



貢獻者克隆並運行 `skillshare install -p` 以立即獲取專案特定的 AI 上下文。



## 工作流程總結



```
Project mode:1. Install skills normally      →  config.yaml skills: auto-updates2. Commit config.yaml via git   →  portable across team members3. Run `skillshare install -p`  →  reproduce on clone4. Run `skillshare sync`        →  distribute to all targetsGlobal mode:1. Install skills normally      →  .metadata.json auto-updates2. Push/pull config via git     →  portable across machines3. Run `skillshare install`     →  reproduce on new machine4. Run `skillshare sync`        →  distribute to all targets
```



## 額外配置



除了技能之外，`config.yaml`還可以宣告**額外**－同步到單獨目錄的非技能資源（規則、指令、提示）。額外配置在 `config.yaml` 的 `extras:` 部分（全域和專案）：



```
extras:  - name: rules    targets:      - path: ~/.claude/rules      - path: ~/.cursor/rules        mode: copy
```



詳情請參閱[同步額外內容](https://skillshare.runkids.cc/docs/reference/commands/sync#sync-extras)。



## 相關



- [安裝指令](https://skillshare.runkids.cc/docs/reference/commands/install) — `skillshare install` 帶參數與不含參數
- [推/拉](https://skillshare.runkids.cc/docs/reference/commands/push) — 透過 git 備份與還原配置
- [專案技能](https://skillshare.runkids.cc/docs/understand/project-skills) — 專案等級清單