# Quick Reference

> Source: https://skillshare.runkids.cc/docs/getting-started/quick-reference

---

# 快速參考



技能共享的命令備忘單。



## 核心指令



|命令 |描述 |
| --- | --- |
|初始化|首次設定 |
|安裝 <來源> |新增技能 |
|卸載<名稱>... |刪除一項或多項技能 |
|清單 |列出所有技能 |
|搜尋 <查詢> |搜尋技能 |
|同步|推播到所有目標 |
|狀態 |顯示同步狀態 |



## 技能管理



|命令 |描述 |
| --- | --- |
|新<姓名> |創造新技能 |
|更新<名稱> |更新技能 (git pull) |
|更新-全部|更新所有追蹤的儲存庫 |
|檢定|檢定技能更新 |
|檢查--json |檢查更新（JSON 輸出）|
|升級|升級 CLI 和內建技能 |
|中心列表 |列出已配置的技能中心 |
|集線器新增 <url> |新增技能中心 |



## 目標管理



|命令 |描述 |
| --- | --- |
|目標清單|列出所有目標 |
|目標<名稱> |顯示目標詳細資料 |
|目標 <姓名> --mode <模式> |更改同步模式 |
|目標新增 <姓名> <路徑> |新增自訂目標 |
|目標刪除<名稱> |安全移除目標 |
|差異[目標] |顯示差異 |



## 額外管理



|命令 |描述 |
| --- | --- |
| extras init <姓名> --target <路徑> |新增一個 extras 條目到 config |
|額外清單 |列出已設定的附加功能以及同步狀態 |
|額外刪除<名稱> |從配置中刪除額外條目 |
| extras <name> --add-target <路徑> |將目標新增至現有的額外條目 |
| extras <name> --remove-target <路徑> |刪除目標（新增 --prune 以刪除同步檔案）|
|額外收集<名稱> |將本機檔案從 extras 目標收集到來源 |



## 代理管理



|命令 |描述 |
| --- | --- |
|代理清單 |列出已安裝的代理 |
|安裝 <source> --kind 代理程式 |僅安裝來自儲存庫的代理程式 |
|安裝 <來源> -a <姓名> |依名稱安裝特定代理程式 |
| uninstall --kind 代理程式 <名稱> |刪除代理程式 |
|同步代理程式 |僅將代理程式同步到目標 |
|檢查代理|檢查代理的更新 |
|審計代理|安全掃描代理|
|啟用 --kind 代理程式 <名稱> |重新啟用已停用的代理程式 |
|禁用 --kind 代理 <名稱> |透過 .agentignore 禁用代理 |



## 同步操作



|命令 |描述 |
| --- | --- |
|同步附加功能 |同步非技能資源（規則、指令等） |
|同步-全部|將技能+附加功能同步在一起|
|收集<目標> |從目標到來源收集技能 |
|收集-全部|從所有目標收集|
|備份[目標] |建立備份 |
|備份--清單|列出備份 |
|還原 <目標> |從備份還原 |
|提交[-m“訊息”]|建立本地 git 提交而不推送 |
|推[-m「訊息」]|提交並推播到 git remote |
|拉|從 git 拉取並同步 |
|垃圾清單|列出軟刪除技能 |
|垃圾恢復<姓名> |恢復軟刪除的技能 |



## 實用程式



|命令 |描述 |
| --- | --- |
|分析|分析上下文視窗使用情況（互動式 TUI）|
|分析 --filter <文字> |按名稱/路徑子字串過濾技能 |
|分析--json | JSON 格式的上下文使用 |
|醫生|診斷問題 |
|醫生--json |診斷問題（CI 的 JSON 輸出）|
|日誌 |查看操作和審核日誌 |
|使用者介面 |在 localhost:19420 上啟動 Web 儀表板 |
|使用者介面-p |在專案模式下啟動 Web 儀表板 |
|完成 <shell> --install |安裝 shell tab-completion (bash/zsh/fish/powershell/nushell) |
|版本 |顯示 CLI 版本 |
|進行測試-docker |運行離線 Docker 沙箱測試 |
|打造遊樂場|啟動 Playground + 輸入 shell（一步）|
|使遊樂場下降|停止並拆除遊樂場 |
| ./scripts/sandbox.sh <cmd> |高級沙箱管理（上/下/shell/重置/狀態/日誌/裸）|
|製作 ui-build |建構前端 |
|進行建置所有 |帶有前端的完整二進位檔案 |



---



## 常見工作流程



### 安裝並同步技能



```
skillshare install anthropics/skills/skills/pdf
skillshare sync
```



### 建立並部署技能



```
skillshare new my-skill
# Edit ~/.config/skillshare/skills/my-skill/SKILL.md
skillshare sync
```



### 跨機器同步



```
# Setup (pick one)
# Interactive (guided prompts)
skillshare init --remote git@github.com:you/my-skills.git
# Non-interactive (no prompts, auto-detect installed targets)
skillshare init --remote git@github.com:you/my-skills.git --no-copy --all-targets --no-skill
# Optional local checkpoint without pushing
skillshare commit -m "Save local skill edits"
# Machine A: push changes
skillshare push -m "Add new skill"
# Machine B: pull and sync
skillshare pull
```



稍後可選（僅當您在設定後安裝額外的 AI CLI 時）：



```
skillshare init --discover
```



透過發現期間的模式覆蓋，只有新加入的目標會受到影響：



```
skillshare init --discover --select cursor --mode copy
```



### 團隊技能分享



```
# Install team repo
skillshare install github.com/team/skills --track
# Install from specific branch (works with or without --track)
skillshare install github.com/team/skills --branch develop --all
skillshare install github.com/team/skills --track --branch develop
# Update from team
skillshare update --all
skillshare sync
```



### 沙盒遊樂場會話



```
make playground          # start + enter shell
skillshare --help
ss status
exit                     # leave shell
make playground-down     # stop container
```



---



## 關鍵路徑



|路徑|描述 |
| --- | --- |
| 〜/.config/skillshare/config.yaml |設定檔|
| 〜/.config/skillshare/skills/.metadata.json |已安裝的技能元資料（自動管理）|
| 〜/.config/skillshare/skills/|技能源碼目錄 |
| 〜/.config/skillshare/agents/ |代理原始碼目錄 |
| ~/.config/skillshare/extras/<姓名>/ |額外來源目錄|
| 〜/.local/state/skillshare/logs/ |操作及審計日誌 |
| 〜/.local/share/skillshare/backups/ |備份目錄 |



---



## 大多數命令上可用的標誌



|旗幟|描述 |
| --- | --- |
| --dry-run，-n |預覽而不進行更改 |
| --幫助，-h |顯示幫助 |



---



## 另請參閱



- [指令參考](https://skillshare.runkids.cc/docs/reference/commands) — 完整指令文檔
- [概念](https://skillshare.runkids.cc/docs/understand) — 核心概念解釋