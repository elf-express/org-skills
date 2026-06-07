# From Existing Skills

> Source: https://skillshare.runkids.cc/docs/getting-started/from-existing-skills

---

# 來自現有技能



您已經擁有分散在 `~/.claude/skills/`、`~/.cursor/skills/` 或其他 AI CLI 目錄中的技能。本指南將它們合併為一個來源，並用符號連結取代原始內容。



```
BEFORE                                  AFTER─────────────────────────────────────────────────────────────────~/.claude/skills/                       Source (one source of truth)  ├── skill-a/                          ~/.config/skillshare/skills/  └── skill-b/                            ├── skill-a/                                          ├── skill-b/~/.cursor/skills/                         ├── skill-c/  ├── skill-b/  (duplicate!)              └── skill-d/  └── skill-c/                                        Targets (symlinked back)~/.codex/skills/                        ~/.claude/skills/ → source  └── skill-d/                          ~/.cursor/skills/ → source                                        ~/.codex/skills/  → source
```

先備份一下

`collect` 改變目標目錄－本地技能被符號連結取代。在收集之前始終運行`skillshare backup`，這樣如果之後出現任何問題，`skillshare restore <target>`可以撤消它。



## 適用哪一條路徑



|您的情況|路徑|
| --- | --- |
|技能僅存在於一個 CLI 中 |單 CLI 遷移 |
|技能分佈在多個 CLI 中 |多 CLI 整合 |
|你已經在其他地方擁有了 git repo 的技能 |連接現有存儲庫 |



---



## 單 CLI 遷移



如果每項技能都存在於一個目標（例如克勞德）中，`init --copy-from` 一步處理它：



```
skillshare init --copy-from claude
skillshare sync
```



`--copy-from claude` 在初始化期間將`~/.claude/skills/` 中的所有技能複製到來源中。隨後的`sync`用指向來源的符號連結取代了原始內容。



---



## 多 CLI 整合



技能分散在多個目標上。從每個目標初始化空、快照，然後`collect`。



```
# 1. Initialize empty
skillshare init --no-copy
# 2. Snapshot every target before mutating it
skillshare backup
# 3. Collect — once for everything, or per target
skillshare collect --all
#   or:
#   skillshare collect claude
#   skillshare collect cursor
# 4. Sync — targets now symlink back to source
skillshare sync
```



`collect` 對每個目標的作用：



1. 將非符號連結的本地技能複製到來源中（跳過技能內的任何`.git/`）。
2. 用指向來源的符號連結取代原始內容。
3. 偵測重複（相同技能名稱出現在多個目標中）並報告，但不覆蓋。



### 解決重複問題



當來源和您從中收集的目標中存在技能時，將跳過並報告目標版本：



```
Warning: skill-b exists in source  Source:  ~/.config/skillshare/skills/skill-b/  Skipped: ~/.cursor/skills/skill-b/
```



手動解決：比較兩個副本，在來源中保留您想要的任何一個，然後單獨保留目標版本（它將被下一個`sync`上的符號鏈接替換）或重新運行`collect --force`（如果目標版本是您希望保留的版本）。



---



## 連接現有的儲存庫



如果您已經在 GitHub 上有一個技能存儲庫（可能來自以前的機器），請不要 `collect` — 只需克隆它：



```
skillshare init --remote git@github.com:you/skills.git --all-targets --no-skill
skillshare sync
```



追蹤的依賴項被 gitignored 並且不會隨克隆一起下來。初始化後重新安裝它們：



```
skillshare install https://github.com/your-company/skills --track --force
skillshare sync
```



---



## 將遷移的來源推送到 git



遷移後，將原始程式碼置於版本控制之下，以便將來的電腦可以以相同的方式恢復它。



```
# Skip this if you already passed --remote during init.
cd ~/.config/skillshare/skills
git remote add origin git@github.com:you/skills.git
skillshare push -m "Initial commit: migrated skills"
```



從此，`skillshare push`和`skillshare pull`在機器之間移動技能。



---



## 驗證



```
skillshare status     # every target should report 'synced'
skillshare list       # all collected skills should appear
skillshare doctor     # diagnostics — broken symlinks, missing targets, etc.
```



## 回滾



因為您先運行了 `backup`，所以 `collect` 是可逆的：



```
skillshare restore claude
skillshare restore cursor
```



每個目標都會返回其預收集狀態 - 真實文件，沒有符號連結。



---



## 另請參閱



- [日常工作流程](https://skillshare.runkids.cc/docs/how-to/daily-tasks/daily-workflow) — 遷移後的日常工作
- [跨機同步](https://skillshare.runkids.cc/docs/how-to/sharing/cross-machine-sync) — 透過 git 同步
- [核心概念](https://skillshare.runkids.cc/docs/understand) — 源與目標如何關聯