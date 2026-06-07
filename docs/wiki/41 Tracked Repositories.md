# Tracked Repositories

> Source: https://skillshare.runkids.cc/docs/understand/tracked-repositories

---

# 追蹤的儲存庫



Git 儲存庫與 `--track` 一起安裝，用於團隊共用和輕鬆更新。

這什麼時候重要？

追蹤儲存庫是組織分配共享技能的方式。使用 `--track` 安裝一次，然後使用單一指令進行更新。變更從維護者的儲存庫流向每個團隊成員。



## 概述



追蹤的儲存庫是克隆到來源中的 git 儲存庫，並保留其 `.git` 目錄。這使得：



- **團隊共享**：每個人都安裝相同的儲存庫
- **簡單更新**：`skillshare update <name>`運行 git pull
- **版本控制**：追蹤您所在的提交



安裝--track

GitHub：團隊/共享技能

來源：_團隊技能/



---



## 常規技能與追蹤儲存庫



|方面|常規技能|追蹤回購 |
| --- | ---| --- |
|來源 |複製到來源 |使用 .git 克隆 |
|更新 |安裝-更新|更新 <名稱> (git pull) |
|字首|無 | _ 字首 |
|嵌套技能 |壓扁|用 __ 壓平 |



---



## 安裝追蹤倉庫



```
skillshare install github.com/team/shared-skills --track
skillshare sync
```



**會發生什麼事：**



1. Repo克隆到`~/.config/skillshare/skills/_team-skills/`
2. `.git`目錄被保留
3.使用主動安裝閾值（`audit.block_threshold`或`--threshold`）對整個儲存庫進行安全審核
4. AI CLI 的嵌套技能被扁平化



如果发现达到阈值，安装将被阻止，除非使用`--force`。在封鎖時，skillshare 會自動刪除複製的儲存庫；如果清理失敗，則命令會報告手動清理的確切路徑。



---



## 底線前綴



追蹤的倉庫以 `_` 為前綴，以區別於常規技能：



```
~/.config/skillshare/skills/├── my-skill/           # Regular skill (no prefix)├── code-review/        # Regular skill└── _team-skills/       # Tracked repo (underscore prefix)
```



---



## 巢狀技能和自動扁平化



技能存儲庫通常將技能組織在資料夾中。 Skillshare 會自動為 AI CLI 展平它們：



```
SOURCE                              TARGET(your organization)                 (what AI CLI sees)────────────────────────────────────────────────────────────_team-skills/├── frontend/│   ├── react/          ───►   _team-skills__frontend__react/│   └── vue/            ───►   _team-skills__frontend__vue/├── backend/│   └── api/            ───►   _team-skills__backend__api/└── devops/    └── deploy/         ───►   _team-skills__devops__deploy/• _ prefix = tracked repository• __ (double underscore) = path separator
```



### 為什麼要自動展平？號



|效益 |說明 |
| --- | --- |
| AI CLI 相容性 |大多數 AI CLI 期望在平面目錄中使用技能，而不是嵌套資料夾 |
|保護組織 |保持原始程式碼中的邏輯資料夾結構，同時滿足 CLI 要求 |
|溯源 |扁平化名稱顯示原始路徑（例如， _team__frontend__react → 來自 _team/frontend/react/） |
|無需手動作業 | Skillshare 在同步期間自動處理轉換 |



**您組織，技能共享適應。 ** 在任何資料夾結構中編寫技能；他們將在任何地方工作。

提示

自動扁平化適用於**所有技能**，而不僅僅是追蹤的儲存庫。您也可以在資料夾中組織您的個人技能。請參閱[用資料夾整理](https://skillshare.runkids.cc/docs/understand/source-and-targets#organize-with-folders-auto-flattening)。



---



## 更新追蹤的儲存庫



### 單一倉庫



```
skillshare update _team-skills
skillshare sync
```



### 所有追蹤的倉庫



```
skillshare update --all
skillshare sync
```



**會發生什麼事：**



```
cd ~/.config/skillshare/skills/_team-skills
git pull origin main
```



**更新期間的安全行為：**



- 更新內容在拉取後進行審核。
- 阻止使用活動閾值（預設為`audit.block_threshold`，或依指令`--threshold`/`-T`覆蓋）。
- TTY模式下，當發現結果達到閾值時，`skillshare update`提示確認；在非 TTY 模式下，它會自動回滾（除非使用`--skip-audit`）。
- 拒絕時，追蹤的儲存庫回滾到先前的提交以保留本機狀態。
- 如果回滾基線捕獲失敗，則出於安全考慮，更新將中止（失敗關閉）。



---



## 卸載



```
skillshare uninstall _team-skills
```



**會發生什麼事：**



1. 檢查未提交的變更（如果發現則發出警告）
2.刪除目錄
3. 接下來`sync`從目標中刪除符號鏈接



---



## 專案模式



追蹤的儲存庫也可以在專案模式下運作。該儲存庫被克隆到 `.skillshare/skills/` 並添加到 `.skillshare/.gitignore` （因此追蹤的儲存庫的 git 歷史記錄不會與專案的 git 衝突）。預設情況下，項目日誌 (`.skillshare/logs/`)、垃圾 (`.skillshare/trash/`) 和備份 (`.skillshare/backups/`) 也會被忽略。



在`.skillshare/.metadata.json`中安裝追蹤的儲存庫自動記錄`tracked: true`，以便新團隊成員透過`skillshare install -p`獲得正確的克隆行為：



```
{  "skills": [    {      "name": "_team-shared-skills",      "source": "github.com/team/shared-skills",      "tracked": true    }  ]}
```



```
# Install tracked repo into project
skillshare install github.com/team/shared-skills --track -p
skillshare sync
# Update via git pull
skillshare update team-skills -p
skillshare sync
# Force update (discard local changes)
skillshare update team-skills -p --force
# Uninstall
skillshare uninstall team-skills -p
```



**目錄結構：**



```
<project-root>/└── .skillshare/    ├── .gitignore           # Contains: logs/, trash/, and skills/_team-skills    └── skills/        └── _team-skills/    # Tracked repo with .git/ preserved            ├── .git/            ├── frontend/ui/            └── backend/api/
```



如果您有意要提交專案日誌，請在`.skillshare/.gitignore`中的託管區塊之後新增`!logs/`和`!logs/*.log`。



嵌套技能會以與全域模式相同的方式自動展平 - `_team-skills/frontend/ui` 在目標中變成 `_team-skills__frontend__ui`。



---



## 自訂名稱



```
skillshare install github.com/team/skills --track --name acme-skills
# Installed as: _acme-skills/
```



`--track --name` 的名稱限制：



- 必須解析為以 `_` 開頭的追蹤儲存庫目錄名稱。
- 不得包含路徑分隔符號（`/`、`\`）或父遍歷（`..`）。
- 克隆之前無效的名稱將被拒絕。



---



## 分支追蹤



您可以追蹤儲存庫的特定分支：



```
skillshare install github.com/team/skills --track --branch frontend
```



追蹤的儲存庫克隆並遵循指定的分支。透過 `skillshare update` 自動從該分支拉取更新。



若要在多個分支上安裝相同的儲存庫，請使用 `--name` 以避免名稱衝突：



```
skillshare install github.com/team/skills --track --branch frontend --name team-frontend
skillshare install github.com/team/skills --track --branch backend --name team-backend
```



Branch 也適用於常規（非追蹤）安裝：



```
skillshare install github.com/team/skills --branch develop --all
```



此分支保留在技能元資料中，因此`skillshare update`和`skillshare check`會自動使用正確的分支。



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



**最佳實務** — 為您的技能命名或使用篩選器：



```
# Option 1: Namespace in SKILL.mdname: team-a-ui# Option 2: Route with filters (global config)targets:  codex:    path: ~/.codex/skills    include: [_team-a__*]  claude:    path: ~/.claude/skills    include: [_team-b__*]
```



```
# Option 2: Route with filters (project config)targets:  - name: claude    exclude: [codex-*]  - name: codex    include: [codex-*]
```



有關完整語法和範例，請參閱[目標過濾器](https://skillshare.runkids.cc/docs/reference/targets/configuration#include--exclude-target-filters)。



---



## 另請參閱



- [安裝](https://skillshare.runkids.cc/docs/reference/commands/install) — 使用`--track` 安裝
- [更新](https://skillshare.runkids.cc/docs/reference/commands/update) — 拉取最新更改
- [檢查](https://skillshare.runkids.cc/docs/reference/commands/check) — 查看可用更新
- [組織範圍的技能](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing) — 團隊分享指南