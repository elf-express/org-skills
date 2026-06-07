# First Sync

> Source: https://skillshare.runkids.cc/docs/getting-started/first-sync

---

# 第一次同步



按順序完成完整的首次設定。從安裝到同步工作大約需要五分鐘。本頁末尾記錄了兩種變體——在另一台機器上恢復，以及在無頭盒子上無人值守地運行。



## 先決條件



- macOS、Linux 或 Windows
- 至少安裝一個 AI CLI（Claude Code、Cursor、Codex 等）



## 1. 安裝 CLI



**自製軟體（macOS / Linux）：**



```
brew install skillshare
```

筆記

自製軟體的發布可能會滯後幾天。對於最新版本，請使用安裝腳本。



**安裝腳本（macOS / Linux）：**



```
curl -fsSL https://raw.githubusercontent.com/runkids/skillshare/main/install.sh | sh
```



**Windows（PowerShell）：**



```
irm https://raw.githubusercontent.com/runkids/skillshare/main/install.ps1 | iex
```

稍後更新

`skillshare upgrade` 偵測您的安裝方式（自製程式、腳本、手冊）並就地更新 CLI。



## 2. 初始化



```
skillshare init
```



![Interactive init flow](https://skillshare.runkids.cc/img/init-with-mode.png)



`init` 引導您完成四個選擇：



1. **來源目錄** — 預設為`~/.config/skillshare/skills/`。按 Enter 接受。
2. **Git Remote** — 貼上您的個人技能儲存庫的 URL（例如 `git@github.com:you/skills.git`）。如果您還沒有，請先在 GitHub 上建立一個空的儲存庫；您也可以跳過並稍後新增遙控器。
3. **目標** — Skillshare 偵測已安裝的 AI CLI 並列出它們。確認或取消選擇您不需要的任何內容。
4. **內建技能** — 可選。新增 `/skillshare` 命令，以便您的 AI CLI 可以直接呼叫 Skillshare。



### 選擇同步模式



`init` 接受 `--mode <merge|copy|symlink>` 來設定新新增目標的預設值：



- `merge`（預設）－每個技能的符號連結；保留現有的目標本地技能
- `symlink` — 整個目標目錄變成符號連結（最快，替換目錄）
- `copy` — 真實文件；更改適用於下一個`sync`



稍後可透過 `skillshare target <name> --mode <mode>` 實現按目標覆蓋。



## 3.安裝技能



```
skillshare install anthropics/skills/skills/pdf
```



每次安裝都會運行安全審核。關鍵發現阻礙了安裝；僅當您審查並接受風險時，才能通過`--force`。



## 4. 同步



```
skillshare sync
```



現在每個配置的目標都指向您的來源。



## 5. 驗證



```
skillshare status
```



輸出應顯示來源路徑、每個標記為`synced`的目標以及您剛安裝的技能。



---



## 剛剛發生了什麼事



1. **`init`** 創建了 `~/.config/skillshare/config.yaml` 和 `~/.config/skillshare/skills/`，自動檢測您的 AI CLI，並且（如果您提供了遙控器）從中克隆任何預先存在的技能。
2. **`install`** 將技能複製到來源目錄並執行安全審核。 `.metadata.json` 記錄上游 URL 並提交，以便 `skillshare update` 可以拉取未來的變更。
3. **`sync`** 應用每個目標的配置模式。例如，在`merge`模式下：

```
~/.claude/skills/pdf → ~/.config/skillshare/skills/pdf  (symlink)
```



在`merge`和`symlink`模式下，對來源的編輯會立即出現在每個目標中。在`copy`模式下，它們適用於下一個`sync`。預先存在的目標本地技能保留在`merge`和`copy`中； `skillshare backup` 破壞性操作之前的快照和 `skillshare restore <target>` 恢復。



只需要針對一個目標使用不同的模式？覆蓋每個目標：



```
skillshare target <name> --mode copy
skillshare sync
```



有關完整的決策矩陣，請參閱[同步模式](https://skillshare.runkids.cc/docs/understand/sync-modes)。



---



## 變更：在另一台機器上恢復



您已經在其他地方使用了 Skillshare，並且在 GitHub 上擁有個人技能儲存庫。在新的筆記型電腦、devcontainer 或虛擬機器上，四個命令可以恢復所有內容 - 沒有提示，沒有選擇，重新運行時冪等：



```
# 1. Install the CLI (Homebrew or curl|sh — same as Step 1 above)
brew install skillshare
# 2. Clone your skills repo and add detected targets
skillshare init \  --remote git@github.com:<you>/skills.git \  --all-targets \  --no-skill
# 3. Re-install tracked dependencies
#    (the _-prefixed dirs are gitignored, so they aren't in the cloned repo)
skillshare install https://github.com/<your-company>/skills --track --force
# 4. Sync
skillshare sync
```



`--no-skill`跳過內建技能提示；如果您想在本機上使用它，請稍後使用 `skillshare upgrade --skill` 添加它。



---



## 變化：無頭設定（無 TTY）



對於 CI 作業、devcontainer 建立後掛鉤或雲端虛擬機器配置程序，每個提示都有一個非互動式標誌：



```
skillshare init \  --source ~/.config/skillshare/skills \  --remote https://github.com/<you>/skills \  --targets codex \  --mode merge \  --no-copy \  --no-skill
skillshare install https://github.com/<your-company>/skills --track --force
skillshare sync
```



|旗幟|效果|
| --- | --- |
| --來源 <路徑> |跳過來源路徑提示 |
| --remote <url> |跳過遠端提示；如果遠端有內容則克隆 |
| --targets <名稱> |僅新增列出的目標（使用 --all-targets 新增每個偵測到的目標）|
| --模式合併|新目標的預設同步模式 |
| --無複製|跳過「複製現有目標技能？」迅速的;從空開始|
| --無技能|跳過內建技能提示 |



`--targets`、`--all-targets` 和 `--no-targets` 是互斥的 — 選擇一個。



---



## 下一步



- [創造自己的技能](https://skillshare.runkids.cc/docs/how-to/daily-tasks/creating-skills)
- [跨機器同步](https://skillshare.runkids.cc/docs/how-to/sharing/cross-machine-sync)
- [全組織技能](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing)
- [特工](https://skillshare.runkids.cc/docs/understand/agents) — 管理單文件`.md`特工以及技能
- [同步模式](https://skillshare.runkids.cc/docs/understand/sync-modes) — 決策矩陣與權衡