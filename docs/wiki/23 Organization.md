# Organization

> Source: https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing

---

# 組織範圍內的技能



使用追蹤存儲庫在所有項目中共享技能。



## 概述



安裝--track

更新

GitHub: your-org/shared-skills

團隊成員的機器

每個人都會收到更新



---



## 使用場景



|場景 |範例|
| ---| ---|
|公司編碼標準|在所有儲存庫中強制執行一致的命名、錯誤處理和架構 |
|安全審計技巧|適用於每個項目的組織範圍內的安全審查清單
|部署知識|標準 CI/CD 模式、基礎架構約定、發布流程 |
|程式碼審查指南 |所有團隊和專案的一致審查標準 |
|跨專案模式|共享API設計模式、日誌標準、測試框架|



---



## 為什麼要組織共享？號



|沒有組織能力|具有組織能力|
| ---| ---|
| 「嘿，從 Slack 獲取最新的部署技能」|技能分享更新—全部 |
|機器之間的複製貼上技巧|一個命令即可安裝所有內容 |
| “你擁有哪個版本的技能？” | 每個人都從同一源同步 |
|分散在文件/儲存庫中的技能 |為組織策劃的一個儲存庫 |



---



## 對於團隊領導



### 第 1 步：建立技能儲存庫



為您的組織的技能建立 GitHub/GitLab/Bitbucket 儲存庫。



```
mkdir org-skills && cd org-skills
git init
# Create skill structure
mkdir -p frontend/ui backend/api devops/deploy
# Add skills
echo "---name: acme-uidescription: Frontend UI patterns---# UI Skill..." > frontend/ui/SKILL.md
git add .
git commit -m "Initial skills"
git push -u origin main
```



### 第 2 步：新增 .skillignore（可選）



如果您的儲存庫具有不應作為技能被發現的內部工具或 CI 腳本，請在儲存庫根目錄中建立一個 `.skillignore`：

.skillignore

```
# CI/CD helpers — not installable skillsci-scripts_internal-*
```



需要 `.skillignore` 阻止的技能的個人團隊成員可以在同一目錄（未提交到 git）中創建一個 `.skillignore.local` 以在本地覆蓋它：

.skillignore.local

```
!_internal-my-tool
```



### 步驟 3：分享安裝指令



將此發送給您的團隊：



```
skillshare install github.com/your-org/org-skills --track && skillshare sync
```



只需要子集的團隊成員可以使用`--exclude`：



```
skillshare install github.com/your-org/org-skills --all --exclude devops-deploy
```



---



## 對於團隊成員



### 初始設定



```
# Install the organization skills repo
skillshare install github.com/org/skills --track
# Sync to your AI CLIs
skillshare sync
```



### 日常使用



```
# Check for updates
skillshare update --all
skillshare sync
```



---



## 巢狀技能和自動扁平化



在資料夾中組織技能 - Skillshare 會自動將它們展平以實現 AI CLI 相容性：



```
SOURCE                              TARGET(your organization)                 (what AI CLI sees)────────────────────────────────────────────────────────────_org-skills/├── frontend/│   ├── react/          ───►   _org-skills__frontend__react/│   └── vue/            ───►   _org-skills__frontend__vue/├── backend/│   └── api/            ───►   _org-skills__backend__api/└── devops/    └── deploy/         ───►   _org-skills__devops__deploy/• _ prefix = tracked repository• __ (double underscore) = path separator
```



**好處：**



- 在您的儲存庫中保留邏輯資料夾組織
- AI CLI 看到了他們期望的扁平結構
- 扁平化名稱保留原始路徑以實現可追溯性



詳情請參閱[追蹤的儲存庫](https://skillshare.runkids.cc/docs/understand/tracked-repositories#nested-skills--auto-flattening)。



---



## 碰撞偵測



當多個技能共用相同的`name`欄位時，同步會在應用`include`/`exclude`過濾器後檢查它們是否確實落在同一目標上。



**過濾器隔離碰撞** — 僅供參考：



```
ℹ Duplicate skill names exist but are isolated by target filters:  'ui' (2 definitions)
```



**碰撞達到相同目標** — 可操作的警告：



```
⚠ Target 'claude': skill name 'ui' is defined in multiple places:  - _team-a/frontend/ui  - _team-b/components/uiRename one in SKILL.md or adjust include/exclude filters
```



**解決方案：** 使用命名空間名稱或帶有過濾器的路由：



```
# Option 1: Namespace in SKILL.md
name: team-a-ui
# Option 2: Route with filters (global config)
targets:  codex:    path: ~/.codex/skills    include: [_team-a__*]  claude:    path: ~/.claude/skills    include: [_team-b__*]
```



```
# Option 2: Route with filters (project config)
targets:  - name: claude    exclude: [codex-*]  - name: codex    include: [codex-*]
```



有關完整語法和範例，請參閱[目標過濾器](https://skillshare.runkids.cc/docs/reference/targets/configuration#include--exclude-target-filters)。



---



## 多個組織儲存庫



為不同的團隊或關注點安裝多個儲存庫：



```
# Frontend team
skillshare install github.com/org/frontend-skills --track --name frontend
# Backend team
skillshare install github.com/org/backend-skills --track --name backend
# DevOps team
skillshare install github.com/org/devops-skills --track --name devops
skillshare sync
```



全部更新：



```
skillshare update --all
skillshare sync
```



---



## 私有儲存庫



**SSH**（建議用於開發人員機器）：



```
skillshare install git@github.com:org/private-skills.git --track
```



**帶有令牌的 HTTPS**（建議用於 CI/CD）：



```
export GITHUB_TOKEN=ghp_your_token
skillshare install https://github.com/org/private-skills.git --track
```



官方代幣文檔：



- GitHub：[管理您的個人存取權杖](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
- GitLab：[代幣概述](https://docs.gitlab.com/security/tokens/)
- Bitbucket：[訪問令牌](https://support.atlassian.com/bitbucket-cloud/docs/access-tokens/)



###CI/CD 設定



**GitHub 操作：**



```
- name: Install org skills  env:    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  run: |
    skillshare install https://github.com/org/skills.git --track
    skillshare sync
```



**GitLab 來自：**



```
install-skills:  script:    - skillshare install https://gitlab.com/org/skills.git --track    - skillshare sync  variables:    GITLAB_TOKEN: $CI_JOB_TOKEN
```



**位桶管道：**



```
- step:    name: Install org skills    script:      - skillshare install https://bitbucket.org/team/skills.git --track      - skillshare sync    env:      BITBUCKET_USERNAME: $BITBUCKET_USERNAME   # for app passwords      BITBUCKET_TOKEN: $BITBUCKET_TOKEN
```



有關所有支援的令牌，請參閱[環境變數](https://skillshare.runkids.cc/docs/reference/appendix/environment-variables#git-authentication)。



---



## 命令參考



|命令|描述 |
| ---| ---|
|安裝 <url> --track |將儲存庫克隆為追蹤儲存庫 |
|更新<名稱> | Git 拉取特定追蹤儲存庫 |
|更新-全部|更新所有追蹤的儲存庫 |
|卸載<名稱>... |刪除追蹤的儲存庫 |
|清單 |列出所有技能和追蹤的儲存庫 |
|狀態 |顯示同步狀態 |



---



## 組織代理



追蹤的組織儲存庫可以隨技能一起運送**代理**。將它們放在 `skills/` 旁邊的頂級 `agents/` 目錄中：



```
your-org/org-shared/├── skills/                  # Discovered as skills│   ├── api-design/│   │   └── SKILL.md│   └── security/│       └── SKILL.md└── agents/                  # Discovered as agents    ├── reviewer.md    └── auditor.md
```



當隊友運行`skillshare install github.com/your-org/org-shared --track`時，兩個目錄都會自動選取。 `skillshare update --all` 保持兩者同步，`skillshare sync`（或 `skillshare sync agents`）將代理傳播到具有代理功能的目標（Claude、Cursor、Augment、OpenCode）。



組織儲存庫內的`.agentignore`檔案在磁碟上受到尊重，但通常應位於消費者的來源根目錄（或`.agentignore.local`）中，以便各個電腦可以選擇退出而無需編輯上游儲存庫。完整的發現規則請參閱[代理](https://skillshare.runkids.cc/docs/understand/agents)。



---



## 組織與專案技能



| |組織技能|專案技能|
| ---| ---| ---|
|範圍 |機器上的所有項目 |單一儲存庫 |
|來源 | 〜/.config/skillshare/skills/_repo/| .skillshare/技能/ |
|安裝 | Skillshare 安裝 <url> --track | Skillshare 安裝 <url> -p |
|分享透過 |每個成員安裝追蹤的儲存庫 |致力於 git repo 專案 |
|最適合 |編碼標準、安全性、組織模式 | API 約定、領域脈絡、專案工具 |
|共存|與專案技能一起工作 |與組織技能一起工作|

兩者同時使用

組織技能提供了全公司範圍的標準。專案技能提供了特定於儲存庫的上下文。它們相輔相成——使用兩者來獲得最佳的開發人員體驗。



---



## 最佳實踐



### 對於團隊領導



1. **使用清晰的結構**：按功能組織（前端、後端、devops）
2. **命名空間技巧**：`org-skill-name`避免碰撞
3. **文件要求**：附有設定說明的自述文件
4. **版本控制**：使用標籤進行穩定版本



### 對於團隊成員



1. **定期更新**：每天`skillshare update --all`
2. **回報問題**：如果某項技能不起作用，請告訴維修人員
3. **提出改進建議**：向技能存儲庫開放 PR



---



## 另請參閱



- [追蹤儲存庫](https://skillshare.runkids.cc/docs/understand/tracked-repositories) — 概念詳細信息
- [安裝](https://skillshare.runkids.cc/docs/reference/commands/install) — 使用`--track` 安裝
- [更新](https://skillshare.runkids.cc/docs/reference/commands/update) — 更新追蹤的儲存庫
- [專案設定](https://skillshare.runkids.cc/docs/how-to/sharing/project-setup) — 專案級共享
- [跨機同步](https://skillshare.runkids.cc/docs/how-to/sharing/cross-machine-sync) — 個人同步