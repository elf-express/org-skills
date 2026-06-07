# Organizing Skills with Folders

> Source: https://skillshare.runkids.cc/docs/how-to/daily-tasks/organizing-skills

---

# 用資料夾組織技能



隨著您的技能收藏不斷增加，將它們組織到資料夾中可以使事情變得易於管理，而 Skillshare 會自動處理其餘的事情。



## 為什麼要組織？號



包含 20 多種技能的簡單清單變得難以瀏覽：



```
~/.config/skillshare/skills/├── accessibility/├── ascii-box-check/├── core-web-vitals/├── frontend-design/├── performance/├── react-best-practices/├── remotion/├── seo/├── skill-creator/├── ui-skills/├── vue-best-practices/├── vue-debug-guides/├── web-artifacts-builder/└── ... 20+ more
```



透過資料夾，您可以進行邏輯分組，同時技能分享會針對 AI CLI 自動展平：



```
SOURCE (organized)                     TARGET (auto-flattened)───────────────────────────────────    ──────────────────────────────────~/.config/skillshare/skills/           ~/.claude/skills/├── frontend/                          ├── frontend__frontend-design│   ├── frontend-design/               ├── frontend__react__react-best-..│   ├── react/                         ├── frontend__ui-skills│   │   └── react-best-practices/      ├── frontend__vue__vue-best-prac..│   ├── ui-skills/                     ├── frontend__vue__vue-debug-gui..│   └── vue/                           ├── utils__ascii-box-check│       ├── vue-best-practices/        ├── utils__remotion│       ├── vue-debug-guides/          ├── utils__skill-creator│       └── ...                        ├── web-dev__accessibility├── utils/                             ├── web-dev__core-web-vitals│   ├── ascii-box-check/               └── ...│   ├── remotion/│   └── skill-creator/└── web-dev/    ├── accessibility/    ├── core-web-vitals/    └── ...
```



![Source vs Target comparison](https://skillshare.runkids.cc/assets/images/organizing-skills-comparison-d5a81c76be51588b80498fcdbdcb3ced.png)

現實世界的例子

請參閱 [runkids/my-skills](https://github.com/runkids/my-skills)，以了解使用此模式的完整有組織的技能集合。



---



## 自動展平如何運作



Skillshare 使用 `__` （雙底線）作為分隔符號將資料夾路徑轉換為平面名稱：



|來源路徑|同步目標名稱 |
| ---| ---|
|前端/反應/反應最佳實踐/ |前端__react__react-最佳實踐 |
| utils/遠端/ | utils__remotion | 實用程式
|網頁開發/輔助功能/ |網頁開發__可訪問性 |



**重點：**



- 只有包含`SKILL.md`的目錄才被視為技能
- 中間資料夾（如 `frontend/` 本身）只是組織性的 - 它們不需要 `SKILL.md`
- `list`和`sync`發現任意深度的嵌套技能
- `check`和`update`也適用於嵌套技能

代理不嵌套

本頁是關於組織**技能**的。代理總是直接放置在`~/.config/skillshare/agents/`（或專案模式下的`.skillshare/agents/`）下的單一`.md`檔案 - 它們不支援資料夾嵌套或自動展平。要組織代理，請使用命名約定（例如`frontend-reviewer.md`、`backend-auditor.md`）和`.agentignore`模式。



---



## 使用嵌套技能



### 列表



同一目錄中的技能會自動分組在一起：



```
$ skillshare list -g  frontend/vue/    → vue-best-practices     github.com/vuejs-ai/skills/...  utils/    → remotion               github.com/remotion-dev/skills/...  web-dev/    → accessibility          github.com/addyosmani/web-quality-...
```



在每個組中，技能顯示其基本名稱（不是完整的單位名稱）。頂級技能未分組地顯示在底部。如果所有技能都是頂級的，則輸出是一個平面列表——與舊格式相同。



###檢查



偵測嵌套技能並顯示相對路徑：



```
$ skillshare check -gChecking for updates─────────────────────────────────────────▸  Source  ~/.config/skillshare/skills│├─ Items  0 tracked repo(s), 15 skill(s)  ✓ frontend/frontend-design            up to date  ✓ frontend/react/react-best-practices up to date  ✓ utils/remotion                      up to date  ✓ web-dev/accessibility               up to date
```



###更新



支援**完整路徑**和**短名稱**：



```
# Full relative path
skillshare update -g frontend/react/react-best-practices
# Short name (basename) — auto-resolved
skillshare update -g react-best-practices
# Update everything
skillshare update -g --all
```



當一個短名稱符合多個技能時，skillshare 會要求您提供更具體的資訊：



```
'my-skill' matches multiple items:  - frontend/my-skill  - backend/my-skillPlease specify the full path
```



---



## 直接安裝到資料夾中



使用`--into`一步將技能安裝到子目錄中 - 無需手動`mv`：



```
# Install into a category folder
skillshare install anthropics/skills -s pdf --into frontend
# → ~/.config/skillshare/skills/frontend/pdf/
# Multi-level nesting
skillshare install ~/my-skill --into frontend/react
# → ~/.config/skillshare/skills/frontend/react/my-skill/
# Works with --track too
skillshare install github.com/team/skills --track --into devops
# → ~/.config/skillshare/skills/devops/_skills/
# Works in project mode
skillshare install anthropics/skills -s pdf --into tools -p
# → .skillshare/skills/tools/pdf/
```



在`skillshare sync`之後，目標顯示自動拼合的名稱：



- `frontend/pdf/` → `frontend__pdf`
- `frontend/react/my-skill/` → `frontend__react__my-skill`
- `devops/_skills/frontend/ui/` → `devops___skills__frontend__ui`

提示

`--into` 自動建立中間目錄。無需先`mkdir`。



---



## 建議的資料夾結構



### 按網域



```
skills/├── frontend/│   ├── react/│   ├── vue/│   └── css/├── backend/│   ├── api-design/│   └── database/├── devops/│   ├── docker/│   └── ci-cd/└── utils/    ├── git-workflow/    └── code-review/
```



### 按工俱生態系統



```
skills/├── vue/│   ├── vue-best-practices/│   ├── vue-debug-guides/│   ├── vue-pinia-best-practices/│   └── vue-router-best-practices/├── react/│   └── react-best-practices/└── web/    ├── accessibility/    ├── performance/    └── seo/
```



### 混合：個人 + 追蹤儲存庫



```
skills/├── frontend/              # Personal organized skills│   └── vue/├── utils/                 # Personal utilities│   └── ascii-box-check/├── _team-skills/          # Tracked repo (auto-updated)│   ├── code-review/│   └── deploy/└── _org-standards/        # Another tracked repo    └── security/
```



---



## 版本控制你的技能



資料夾中的組織技能與 git 自然搭配：



```
skillshare init --remote git@github.com:yourname/my-skills.git
skillshare push -m "organize skills into categories"
```



這給你：



- 跨機器技能變化的**歷史**
- 透過 GitHub/GitLab **備份**
- **分享** — 其他人可以瀏覽和分叉您的收藏
- **跨機同步**透過`skillshare pull`（請參閱[跨機同步](https://skillshare.runkids.cc/docs/how-to/sharing/cross-machine-sync)）



---



## 從平面遷移到資料夾

新安裝

對於新技能，請使用 `--into` 直接安裝到正確的資料夾中 - 請參閱上面的直接安裝到資料夾中。



如果您已經擁有扁平技能集合：



```
cd ~/.config/skillshare/skills
# Create category folders
mkdir -p frontend/react frontend/react utils web-dev
# Move skills into folders
mv react-best-practices frontend/react/
mv react-debug-guides frontend/react/
mv react-best-practices frontend/react/
mv remotion utils/
mv accessibility web-dev/
# Re-sync to update target symlinks
skillshare sync
```



在`sync`之後，目標會自動更新－舊的平面符號連結被清理並建立新的平面名稱。



---



## 另請參閱



- [源與目標](https://skillshare.runkids.cc/docs/understand/source-and-targets) — 扁平化的工作原理
- [追蹤儲存庫](https://skillshare.runkids.cc/docs/understand/tracked-repositories) — 儲存庫中的嵌套技能
- [最佳實踐](https://skillshare.runkids.cc/docs/how-to/daily-tasks/best-practices) — 命名約定
- [安裝](https://skillshare.runkids.cc/docs/reference/commands/install) — 使用 `--into` 安裝子目錄