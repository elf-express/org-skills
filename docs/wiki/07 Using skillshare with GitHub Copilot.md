# Using skillshare with GitHub Copilot

> Source: https://skillshare.runkids.cc/docs/learn/with-copilot

---

# 將技能分享與 GitHub Copilot 一起使用



> 從安裝到第一次同步 — 5 分鐘。



## 先決條件



- [GitHub Copilot](https://github.com/features/copilot) 在 VS Code 或 JetBrains 中啟用編碼代理
- macOS、Linux 或 Windows



## 第 1 步：安裝技能共用



```
curl -fsSL https://raw.githubusercontent.com/runkids/skillshare/main/install.sh | sh
```



## 第 2 步：初始化



```
skillshare init
```



這會偵測 Copilot 的技能目錄 (`~/.copilot/skills/`) 並自動將其新增為目標。



## 步驟 3：切換到複製模式（建議）



我們收到報告稱 Copilot 有時無法正確追蹤符號連結。為避免出現問題，請將 Copilot 目標切換到 **複製模式**：



```
skillshare target copilot --mode copy
```



複製模式將技能文件物理複製到`~/.copilot/skills/`而不是建立符號連結。權衡是來源編輯不會立即反映 - 您需要執行 `skillshare sync` 來傳播變更。但它在跨平台上更可靠。

何時使用合併（符號連結）模式

如果您使用的是 macOS 或 Linux，並且 Copilot 在您的電腦上正確讀取符號鏈接，則預設合併模式可以正常工作。您可以隨時切換回來：

```
skillshare target copilot --mode merge
```



## 第 4 步：安裝你的第一個技能



```
skillshare install runkids/my-skills
```



## 步驟 5：同步



```
skillshare sync
```



技能複製到`~/.copilot/skills/`。副駕駛按照自訂指令拿起它們。



## 第 6 步：驗證



```
ls ~/.copilot/skills/
```



您應該將安裝的技能視為真實目錄（在複製模式下）或符號連結（在合併模式下）。



## 副駕駛特定註解



- **技能路徑**：`~/.copilot/skills/`（全域）或`.github/skills/`（專案）
- **代理路徑**：`~/.copilot/agents/`（全域）或 `.github/agents/`（專案）- Copilot CLI 以 Skillshare 管理的相同 `.agent.md` 格式讀取自訂代理，因此 `skillshare sync agents` 無需轉換即可分發它們。請參閱[代理](https://skillshare.runkids.cc/docs/understand/agents)。
- **專案模式**：運行 `skillshare init -p` 來管理專案級 Copilot 技能 - 它們與您的程式碼庫一起進入 `.github/skills/`
- **符號連結問題**：如果 Copilot 沒有拾取您的技能，請檢查您的目標是否處於合併模式 (`skillshare status`) 並切換到複製模式，如上所述
- **`.github/copilot-instructions.md`**：如果您有現有的說明文件，技能共享技能將對其進行補充 - 它們不會取代它



## 下一步是什麼？號



- [管理多種技巧→](https://skillshare.runkids.cc/docs/how-to/daily-tasks/organizing-skills)
- [與你的團隊分享→](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing)
- [探索更多技巧→](https://skillshare.runkids.cc/docs/reference/commands/search)