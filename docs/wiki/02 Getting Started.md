# Getting Started

> Source: https://skillshare.runkids.cc/docs/getting-started/first-sync

---

＃ 入門



Skillshare 讓一個來源目錄與電腦上每個 AI CLI 的技能目錄保持同步。您編寫或安裝一項技能一次；符號連結使其出現在 Claude、Cursor、Codex 和任何其他配置的目標中。



〜/.config/skillshare/技能/
（你的 Git 儲存庫）

〜/.克勞德/技能/

〜/.cursor/技能/

〜/.codex/技能/



來源是您擁有的常規 Git 儲存庫。從一台機器上推送，在另一台機器上拉動，與隊友共用 - Skillshare 處理下面的符號連結層。



## 原始碼中存在什麼



三種技能在源目錄中共存。它們的不同之處僅在於 Git 追蹤它們的方式以及更新它們的方式。



**您創作的技能。 ** 使用 `skillshare new <name>` 建立或只是放入一個資料夾。致力於您的儲存庫。透過編輯進行更新。



**出售的技能。 ** 使用 `skillshare install <url>` 安裝。克隆直接存在於您的存儲庫中，並與您自己的工作一起提交。 `.metadata.json` 記錄上游 URL，以便`skillshare update` 可以稍後拉取新版本。當您想要自訂、凍結版本或保持離線可重現性時，請使用此選項。



**追蹤技能。 ** 與`skillshare install <url> --track`一起安裝。克隆位於以 `_` 為前綴的目錄中，該目錄會自動添加到 `.gitignore`，因此它永遠不會進入您的儲存庫。 `skillshare update`從上游重新拉取它。將此用於您不打算修改的公司或社群儲存庫。



幾個月後的典型來源如下：



```
~/.config/skillshare/skills/├── my-review/                 # authored├── my-deploy-checklist/       # authored├── agent-browser/             # vendored├── skill-creator/             # vendored├── _company-skills/           # tracked  (gitignored)└── _team-rules/               # tracked  (gitignored)
```



## 選擇一個起點



|你是… |從這裡開始 |
| --- | --- |
|首次設定技能共享 |第一次同步 |
|已具備 Claude / Cursor / Codex 技能 |來自現有技能|
|需要快速指令語法 |快速參考|
|想在不安裝的情況下摸索| Docker 遊樂場 |



## 下一步



- [核心概念](https://skillshare.runkids.cc/docs/understand) — 深入了解來源、目標、同步模式
- [日常工作流程](https://skillshare.runkids.cc/docs/how-to/daily-tasks/daily-workflow) — 日常使用
- [指令參考](https://skillshare.runkids.cc/docs/reference/commands) — 完整指令參考