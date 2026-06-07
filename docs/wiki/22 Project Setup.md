# Project Setup

> Source: https://skillshare.runkids.cc/docs/how-to/sharing/project-setup

---

# 項目設定



從頭開始設定專案級技能 - 技能範圍僅限於單一儲存庫，透過 git 與您的團隊共用。



## 何時使用專案模式



|場景 |範例|使用|
| ---| ---| ---|
| Monorepo 入門 |新員工複製儲存庫，立即取得所有專案情境 |專案模式|
| API 約定 | 「所有端點必須使用駝峰命名法並傳回標準錯誤格式」 |專案模式|
|特定領域的脈絡 |金融監理規則、醫療保健合規指南|專案模式|
|部署知識| “透過 make deploy-staging 部署到暫存，需要 VPN”|專案模式|
|專案工具|自訂測試模式、遷移腳本、建置配置 |專案模式|
|所有專案共享的技能 |全公司編碼標準、安全審計|組織模式|
|多台機器上的個人技能|個人格式首選項、工作流程捷徑 |全域模式|



---



## 逐步設定



### 第 1 步：初始化



在專案根目錄中運行 `skillshare init -p`：



```
cd my-project
skillshare init -p
```



技能共享初始化-p

1. 創建.skillshare/目錄2。檢測 AI CLI 目錄3。建立目標技能目錄4。編寫config.yaml自動檢測

初始化後，每當您 `cd` 進入此目錄時，skillshare 都會自動偵測專案模式。後續指令不需要 `-p` 標誌。



您也可以直接指定目標：



```
skillshare init -p --targets claude,cursor
```



### 第 2 步：培養本地技能



手動或使用`skillshare new`創建技能：



```
# Using skillshare new
skillshare new my-skill -p
# Or manually
mkdir -p .skillshare/skills/my-skill
cat > .skillshare/skills/my-skill/SKILL.md << 'EOF'
---
name: my-skill
description: Project-specific coding guidelines
---
# My Skill
Your skill content here...
EOF
```



### 第 3 步：安裝遠端技能



將 GitHub 上的技能安裝到專案中：



```
skillshare install anthropics/skills/skills/pdf -p
skillshare install github.com/team/shared-skills/review -p
# Organize into subdirectories with --into
skillshare install anthropics/skills -s pdf --into tools -p
# → .skillshare/skills/tools/pdf/
```



遠距技能有：



- 安裝到`.skillshare/skills/<name>/`（或`.skillshare/skills/<into>/<name>/`與`--into`）
- 錄製於`.skillshare/config.yaml`下`skills:`
- 新增到`.skillshare/.gitignore`（克隆內容未提交；`logs/`、`trash/`和`backups/`預設被忽略）



### 第 4 步：同步到目標



```
skillshare sync
```



建立從 `.skillshare/skills/` 到每個目標目錄的符號連結。自動檢測項目模式。



### 第 5 步：致力於版本控制



```
git add .skillshare/
git commit -m "Add project-level skills"
```



**承諾什麼：**



- `.skillshare/config.yaml` — 目標與遠端技能列表
- `.skillshare/.gitignore` — 忽略專案日誌、垃圾、備份和複製技能的模式
- `.skillshare/skills/<local-skills>/` — 本地技能內容



**被忽略的內容：**



- `.skillshare/logs/`（操作與稽核日誌）
- `.skillshare/trash/`（軟刪除技能，7天後自動清理）
- `.skillshare/backups/`（來自同步和備份指令的代理備份）
- 遠端技能目錄（從設定重新安裝）



### 可選：提交日誌檔案



如果您希望專案日誌處於版本控制中，請在`.skillshare/.gitignore`中新增覆蓋規則：



```
# User override: track logs
!logs/
!logs/*.log
```



如果 root `.gitignore` 忽略了 `.skillshare/`，也在那裡加入對應的 unignore 規則。



---



## 新團隊成員入職



### 沒有技能分享



1. 克隆倉庫
2.閱讀README以找到要安裝的技能
3.手動複製或安裝每個技能
4. 單獨配置每個AI CLI工具
5.希望你沒有錯過任何事情



### 透過技能分享



```
git clone github.com/team/my-projectcd my-projectskillshare install -p && skillshare sync
```



完畢。所有專案技能均已安裝並同步。 `skillshare install -p`（無 URL）讀取 `.skillshare/config.yaml` 並自動安裝所有列出的遠端技能。相同的模式在全域模式下工作 - `skillshare install`（無參數）讀取`~/.config/skillshare/config.yaml`。



---



## 自訂目標路徑



目標支援已知名稱和自訂路徑：



```
# .skillshare/config.yaml
targets:
  - claude                    # Known name → .claude/skills/
  - cursor                         # Known name → .cursor/skills/
  - name: custom-tool              # Custom path
    path: ./tools/ai/skills        # Relative to project root
  - name: another-tool
    path: ~/global/path/skills     # Absolute path with ~ expansion
```



---



## 完整設定範例



```
targets:
  - claude
  - cursor
  - name: windsurf
    path: .windsurf/skills
skills:
  - name: pdf
    source: anthropic/skills/pdf
  - name: code-review
    source: github.com/team/skills/code-review
```



---



## 網路儀表板



Web 儀表板支援專案模式 - 直覺地管理技能、目標、同步和配置：



```
cd my-projectskillshare ui -p
```



或如果 `.skillshare/config.yaml` 存在（自動偵測到），則簡單地為 `skillshare ui`。



在專案模式下，儀表板：



- 在側邊欄中顯示**「項目」徽章**
- 隱藏 **Git Sync** （使用您專案自己的 git）
- 在設定頁面編輯**`.skillshare/config.yaml`**
- 安裝遠端技能後自動**協調** `skills:`條目



---



## 與全域模式共存



專案和全局（組織）技能獨立發揮作用：



```
Organization level                  Project level~/.config/skillshare/skills/        .skillshare/skills/├── personal-skill/                 ├── project-skill/└── _company-std/                   └── remote-skill/         │                                   │         ▼                                   ▼   ~/.claude/skills/                .claude/skills/   (system-wide targets)            (project-local targets)
```



- 專案目標是**專案本地**（例如，專案內部的`.claude/skills/`）
- 組織目標是**系統範圍**（例如，`~/.claude/skills/`）
- 它們不衝突－不同的目錄，不同的範圍



### 現實世界範例：愛麗絲的兩個專案



Alice 負責開發一個金融應用程式和一個行銷儀表板。她有：



- **組織技能**：公司編碼標準、安全審計（隨處可用）
- **金融專案技能**：監管合規性、金融 API 約定
- **行銷專案技能**：分析模式、A/B 測驗指南



```
cd ~/finance-app
skillshare status     # Shows finance project skills + org skills in system-wide targets
cd ~/marketing-dash
skillshare status     # Shows marketing project skills + same org skills
```



每個專案都有自己的背景，而組織標準適用於全球。



---



## 另請參閱



- [專案技巧](https://skillshare.runkids.cc/docs/understand/project-skills) — 概念解釋
- [專案工作流程](https://skillshare.runkids.cc/docs/how-to/daily-tasks/project-workflow) — 日常使用
- [組織範圍的技能](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing) — 團隊共享
- [init](https://skillshare.runkids.cc/docs/reference/commands/init) — 使用 `--project` 進行初始化