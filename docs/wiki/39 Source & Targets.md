# Source & Targets

> Source: https://skillshare.runkids.cc/docs/understand/source-and-targets

---

# 來源和目標



技能共享背後的核心模型：一個來源，多個目標。

這什麼時候重要？

了解來源與目標可以幫助您了解在哪裡編輯技能和代理（始終在來源中 - 更改透過符號連結反映）、為什麼`sync`是一個單獨的步驟，以及`collect`如何反向工作。



## 問題



如果沒有技能共享，您可以單獨管理每個 AI CLI 的技能：



```
~/.claude/skills/         # Edit here  └── my-skill/~/.cursor/skills/         # Copy to here  └── my-skill/           # Now out of sync!~/.codex/skills/          # And here  └── my-skill/           # Also out of sync!
```



**痛點：**



- 在一處進行的編輯不會傳播
- 技能會隨著時間的推移而逐漸消失
- 沒有單一的事實來源



---



## 解決方案



Skillshare 引入了一個同步到所有 **目標** 的 **來源目錄**：



同步

同步

同步

來源 - ~/.config/skillshare/skills/

〜/.克勞德/技能/

〜/.cursor/技能/

〜/.codex/技能/



**好處：**



- 在原始碼中編輯→所有目標立即更新
- 在目標中編輯 → 變更前往來源（透過符號連結）
- 單一事實來源



---



## 為什麼同步是一個單獨的步驟



`install`、`update`和`uninstall`等操作僅修改**source**目錄。單獨的 `sync` 步驟將變更傳播到所有目標。這種兩階段設計是有意為之的：



**傳播前預覽** — 在應用程式之前執行 `sync --dry-run` 查看所有目標將發生的變化。在`uninstall`或`--force`操作後特別有用。



**批次多項變更** — 安裝 5 個技能，然後同步一次。如果不進行分離，每次安裝都會觸發所有目標的全面掃描和符號連結更新。



**預設安全性** — 原始碼變更是分階段進行的，不會立即生效。您可以控制目標何時更新。此外，`uninstall`將技能移至垃圾目錄（保留7天）而不是永久刪除它們，因此意外刪除是可以恢復的。

例外：拉

`pull` 在`git pull` 之後自動運作同步。由於其目的是“從遠端更新所有內容”，因此自動同步符合預期行為。

當不需要同步時

編輯現有技能不需要同步 - 符號連結意味著更改在所有目標中立即可見。只有當技能集發生變化（新增、刪除、重新命名）或目標/模式變更時，您才需要同步。



---



## 來源目錄



**預設位置：** `~/.config/skillshare/skills/`



這是哪裡：



- 您建立和編輯技能
- 技能被安裝到
- Git 追蹤變更（用於跨機器同步）

符號連結來源目錄

來源目錄可以是符號連結－在使用點檔案管理器（GNU Stow、chezmoi、yadm）時很常見。例如，`~/.config/skillshare/skills/ → ~/dotfiles/ss-skills/`。 Skillshare 在掃描之前解析符號鏈接，因此所有命令都可以透明地工作。也支援鍊式符號連結。



**結構：**



```
~/.config/skillshare/skills/├── my-skill/│   └── SKILL.md├── code-review/│   └── SKILL.md├── _team-skills/          # Tracked repo (underscore prefix)│   ├── frontend/│   │   └── ui/│   └── backend/│       └── api/└── ...
```



### 使用資料夾進行組織（自動拼合）



您可以使用資料夾來組織自己的技能 - 當同步到目標時它們將自動扁平化：



汽車公寓

來源（整理）

目標（扁平化）



**好處：**



- 按專案、團隊或類別組織技能
- 無需手動壓平
- AI CLI 獲得了他們期望的扁平結構
- 資料夾名稱成為可追溯性的前綴



---



## 代理來源



代理是与技能并行的资源。它們位於 `skills/` 旁邊自己的源目錄中，並遵循相同的源和目標模型：



```
~/.config/skillshare/├── skills/                    # Skills source (directories)│   └── my-skill/│       └── SKILL.md└── agents/                    # Agents source (single .md files)    ├── reviewer.md    └── auditor.md
```



相同的 `skillshare init` 運行會建立兩個目錄。代理程式是單一 `.md` 檔案（無嵌套目錄），並透過 `skillshare sync` 同步（或 `skillshare sync agents` 僅適用於代理）。



**支援代理的目標。 ** 並非每個 AI CLI 都會公開代理目錄。這樣做的目標是：



- `~/.claude/agents/` — 克勞德代碼
- `~/.cursor/agents/` — 遊標
- `~/.augment/agents/` — 增強
- `~/.config/opencode/agents/` — 開放式程式碼



在代理同步期間，其他目標會被靜默跳過（帶有 `target(s) skipped for agents (no agents path)` 警告）。適用於技能的相同合併/複製/符號連結模式也適用於代理。



有關完整代理文件格式、`.agentignore` 規則和發現語義，請參閱[代理](https://skillshare.runkids.cc/docs/understand/agents)。



---



## 自訂來源目錄



預設情況下，全域模式從`~/.config/skillshare/skills/`讀取技能，從`~/.config/skillshare/agents/`讀取代理，並從技能來源衍生額外父級。自 v0.19.16 起，可選的頂級 `sources` 映射可讓您覆蓋其中任何一個：



```
# ~/.config/skillshare/config.yamlsources:  skills: ~/work/skills  agents: ~/work/agents  extras: ~/work/extrastargets:  claude:    skills:      path: ~/.claude/skills
```



每個鍵都是可選的 - 省略一個鍵以保持其內建預設值。路徑支援`~`（主擴展）和絕對路徑。



**常見佈局：**



```
# Point all three at a shared dotfiles directorysources:  skills: ~/dotfiles/skillshare/skills  agents: ~/dotfiles/skillshare/agents  extras: ~/dotfiles/skillshare/extras# Override only skills; agents and extras keep their defaultssources:  skills: ~/projects/team-skills
```



### 向後相容性



v0.19.16 之前的頂級字段仍然被接受並繼續保持不變：



```
# Legacy format — fully supported, no auto-migration on savesource: ~/.config/skillshare/skillsagents_source: ~/.config/skillshare/agentsextras_source: ~/.config/skillshare/extras
```



當兩種格式都存在時，`sources.<key>`值勝過對應的舊欄位。現有配置永遠不會自動重寫；只有新的 `skillshare init` 運行才會發出新的 `sources:` 形狀。



### 當這很重要時



在專案模式中也存在相同的功能（專案模式形式請參見[專案技巧](https://skillshare.runkids.cc/docs/understand/project-skills#custom-source-directories)，它也支援從專案根目錄開始的相對路徑）。



---



## 目標



目標是 Skillshare 同步到的 AI CLI 技能目錄。



**共同目標：**



- `~/.claude/skills/` — 克勞德代碼
- `~/.cursor/skills/` — 遊標
- `~/.codex/skills/` — OpenAI Codex CLI
- `~/.gemini/skills/` — 反重力
- 還有[64+更多](https://skillshare.runkids.cc/docs/reference/targets/supported-targets)



**自動偵測：** 當您執行`skillshare init`時，它會自動偵測已安裝的AI CLI並將其新增為目標。



**手動新增：**



```
skillshare target add myapp ~/.myapp/skills
```



---



## 同步如何運作



### 來源 → 目標 (`sync`)



```
skillshare sync
```



建立從每個目標到來源的符號連結：



```
~/.claude/skills/my-skill → ~/.config/skillshare/skills/my-skill
```



### 目標 → 來源 (`collect`)



```
skillshare collect claude
```



將本地技能從目標收集回源：



1. 找出目標中非符號連結的技能
2. 將它們複製到來源（自動排除`.git/`目錄）
3.用符號連結替換



---



## 編輯技巧



由於目標符號連結到來源，因此您可以從任何地方進行編輯：



**在原始碼中編輯：**



```
$EDITOR ~/.config/skillshare/skills/my-skill/SKILL.md# Changes visible in all targets immediately
```



**在目標中編輯：**



```
$EDITOR ~/.claude/skills/my-skill/SKILL.md# Changes go to source (same file via symlink)
```



---



## 另請參閱



- [sync](https://skillshare.runkids.cc/docs/reference/commands/sync) — 將變更從來源傳播到目標
- [收集](https://skillshare.runkids.cc/docs/reference/commands/collect) — 將技能從目標拉回源頭
- [同步模式](https://skillshare.runkids.cc/docs/understand/sync-modes) — 檔案如何連結（合併、複製、符號連結）
- [Agents](https://skillshare.runkids.cc/docs/understand/agents) — Agent 資源模型與發現
- [配置](https://skillshare.runkids.cc/docs/reference/targets/configuration) — 目標配置參考