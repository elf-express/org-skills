# Best Practices

> Source: https://skillshare.runkids.cc/docs/how-to/daily-tasks/best-practices

---

# 最佳實踐



技能的命名約定、組織和版本控制。



## 我們的



### 技能名稱



**做：**



- 使用小寫字母和連字符：`code-review`、`pdf-tools`
- 具有描述性：`react-component-generator` 而非`rcg`
- 團隊命名空間：`acme-code-review`



**不：**



- 使用空格或特殊字符
- Use generic names: `helper`, `utils`, `tools`
- 與常用技能名稱衝突



### 儲存庫名稱



**對於個人：**



```
my-skillsai-skills
```



**對於團隊：**



```
<team>-skills<org>-skills
```



---



## 組織



### 個人技能



```
~/.config/skillshare/skills/├── code-review/├── pdf-tools/├── git-workflow/└── _team-skills/      # Tracked repo
```



### 團隊儲存庫



```
team-skills/├── frontend/│   ├── react/│   ├── vue/│   └── testing/├── backend/│   ├── api/│   └── database/├── devops/│   ├── deploy/│   └── monitoring/└── README.md
```



###技能目錄



```
my-skill/├── SKILL.md           # Required├── README.md          # Optional: for humans├── examples/          # Optional: example files└── templates/         # Optional: code templates
```



---



## 版本控制



### 提交訊息



遵循常規提交：



```
feat(code-review): add security checkfix(pdf-tools): handle empty filesdocs(readme): update installation
```



### 分支



**對於個人：**



- 單一`main`分支就可以
- 使用分支進行實驗



**對於團隊：**



- `main`技能穩定
- 用於開發的功能分支
- 合併前的公關審查



### 標籤



標記穩定版本：



```
git tag v1.0.0
git push --tags
```



---



## 技能寫作



### 結構



```
---name: skill-namedescription: One-line description---# Skill NameBrief overview.## When to UseClear trigger conditions.## Instructions1. Step one2. Step two## ExamplesConcrete input/output examples.## When NOT to UseExplicit exclusions.
```



### 許可證



新增用於發布技能的 `license` 欄位 — 對於企業環境尤其重要：



```
---name: code-reviewdescription: Reviews code for qualitylicense: MIT---
```



這會在`skillshare install`期間顯示，以便用戶可以做出明智的合規決策。



### 內容



**做：**



- 寫出清晰、可操作的指令
- 包括範例
- 指定邊緣情況
- 保持專注（一項技能 = 一個目的）



**不：**



- 寫下模糊的指示
- 包含太多的責任
- 忘記錯誤處理
- 跳過測試



---



## 團隊協作



### 使用專案模式 (`-p`) 取得特定於倉庫的技能



當技能與一個程式碼庫（架構、領域規則、部署流程）緊密耦合時，偏好專案模式：



```
skillshare init -p
skillshare install <source> -p
skillshare sync
```



**為什麼這有幫助：**



- **可重複的入門**：`.skillshare/config.yaml` 充當任何克隆存儲庫的人的便攜式技能清單。
- **範圍清晰**：專案技能保留在`.skillshare/skills/`，而不是滲透到全球個人工作流程。
- **更安全的協作**：透過正常的 git PR 流程和專案程式碼來檢視變更。
- **提交中的噪音較低**：在專案模式下預設忽略`.skillshare/logs/`。



使用全局模式來提高個人跨專案技能；將 `-p` 用於特定於儲存庫的團隊上下文。



### 使用 .skillignore 作為內部工具



如果您的團隊儲存庫包含內部工具或正在進行的技能，請新增 `.skillignore` 以防止意外發現：

.skillignore

```
# Hide from public discovery_internal-scriptstest-*wip-feature
```



這確保了外部貢獻者或運作`skillshare install <repo> --all`的自動化不會獲得內部技能。



**使用 `.skillignore.local`** 進行本地覆蓋：如果共享存儲庫的 `.skillignore` 阻止了您在本地需要的技能，請在同一目錄中創建一個 `.skillignore.local` 來覆蓋它，而無需修改共享文件：

_team-skills/.skillignore.local

```
# Un-ignore my own private skill!private-mine
```



將 `.skillignore.local` 加入您的 `.gitignore` — 它意味著保持本地化。



### 所有權



- 將所有者指派給技能類別
- 自述文件中的文件誰維護什麼
- 合併前審查 PR



### 文檔



```
team-skills/├── README.md           # Setup instructions├── CONTRIBUTING.md     # How to add skills├── CHANGELOG.md        # What changed└── skills/    └── ...
```



### 溝通



- 在團隊聊天中宣布新技能
- 記錄重大變更
- 收集用戶的回饋



---



## 維護



### 常規任務



```
# Weekly
skillshare update --all     # Update tracked repos
skillshare doctor           # Check for issues
skillshare backup --cleanup # Remove old backups
# Monthly
skillshare list             # Review installed skills
# Remove unused: skillshare uninstall <name>...
```



### 清理未使用的技能



```
# List all skills
skillshare list
# Remove ones you don't use
skillshare uninstall unused-skill
skillshare sync
```



### 更新依賴項



```
# Update CLI
skillshare upgrade --cli
# Update built-in skill
skillshare upgrade --skill
# Update tracked repos
skillshare update --all
```



---



## 安全



### 敏感資訊



**永遠不要投入技能：**



- API 金鑰
- 密碼
- 個人資訊
- 內部網址



**反而：**



- 使用環境變數
- 參考外部配置
- 保持技能通用



### 安裝前檢查



安裝第三方技能之前：



- 檢查來源
- 閱讀技能.md
- 先使用`--dry-run`



有關全面的安全工作流程，請參閱[保護您的技能](https://skillshare.runkids.cc/docs/how-to/advanced/security)指南。



---



## 清單



### 新技能



- 描述性名稱
- 清晰的描述
- 可操作的指示
- 包括範例
- 在 AI CLI 中測試
- 沒有名稱衝突



### 團隊回購



- 清晰的資料夾結構
- 附有設定說明的自述文件
- 命名空間技能名稱
- `.skillignore` 用於內部工具
- 公關審核流程
- 維護變更日誌



---



## 另請參閱



- [創造技能](https://skillshare.runkids.cc/docs/how-to/daily-tasks/creating-skills) — 技能創造指南
- [技能設計](https://skillshare.runkids.cc/docs/understand/philosophy/skill-design) — 複雜度等級、確定性、CLI 包裝模式
- [技能格式](https://skillshare.runkids.cc/docs/understand/skill-format) — SKILL.md 參考
- [組織範圍的技能](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing) — 團隊共享模式