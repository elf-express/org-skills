# Using skillshare with Multiple AI Tools

> Source: https://skillshare.runkids.cc/docs/learn/with-multiple-tools

---

# 將技能共享與多種人工智慧工具結合使用



> 一個事實來源，同步到您使用的每個 AI CLI。



## 問題



您在工作中使用 Claude Code，在業餘專案中使用 Cursor，在實驗中使用 Codex。每個都有自己的技能目錄。手動保持它們同步既乏味又容易出錯。



## 解決方案



Skillshare 維護一個來源目錄，並透過一個指令同步到所有目標。



## 第 1 步：安裝並初始化



```
curl -fsSL https://raw.githubusercontent.com/runkids/skillshare/main/install.sh | sh
skillshare init
```



`init` 自動偵測所有已安裝的 AI 工具並將其新增為目標。



## 第 2 步：檢查您的目標



```
skillshare target list
```



輸出範例：



```
  claude       ~/.claude/skills (merge)  cursor       ~/.cursor/skills (merge)  codex        ~/.codex/skills (merge)  opencode     ~/.config/opencode/skills (merge)
```



## 第三步：安裝技能



```
skillshare install runkids/my-skills
skillshare install anthropics/courses/prompt-eng
```



## 第 4 步：同步所有內容



```
skillshare sync
```



一個指令將所有技能推送到每個目標。每個目標都會獲得指向您的單一來源的符號連結。



## 第 5 步：驗證



```
skillshare status
```



顯示所有目標的同步狀態 - 哪些技能已同步、遺失或過期。



## 每目標模式控制



不同的工具有不同的需求。您可以為每個目標設定同步模式：



```
# Cursor follows symlinks fine (default)
skillshare target cursor --mode merge
# Some tools need real files
skillshare target opencode --mode copy
```



## 下一步是什麼？號



- [了解同步模式→](https://skillshare.runkids.cc/docs/understand/sync-modes)
- [跨機同步→](https://skillshare.runkids.cc/docs/how-to/sharing/cross-machine-sync)
- [團隊分享→](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing)