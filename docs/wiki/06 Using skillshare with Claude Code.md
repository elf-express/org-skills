# Using skillshare with Claude Code

> Source: https://skillshare.runkids.cc/docs/learn/with-claude-code

---

# 將技能共享與克勞德代碼一起使用



> 從安裝到第一次同步 — 5 分鐘。



## 先決條件



- [克勞德代碼](https://docs.anthropic.com/en/docs/claude-code/overview)已安裝並運作
- macOS、Linux 或 Windows (WSL)



## 第 1 步：安裝技能共用



```
curl -fsSL https://raw.githubusercontent.com/runkids/skillshare/main/install.sh | sh
```



## 第 2 步：初始化



```
skillshare init
```



這會偵測 Claude Code 的技能目錄 (`~/.claude/skills/`) 並自動將其新增為目標。



## 第 3 步：安裝你的第一個技能



```
skillshare install anthropics/courses/prompt-eng
```



該技能將被下載、審核安全性並添加到您的來源目錄中。



## 步驟 4：同步



```
skillshare sync
```



這會建立從來源到`~/.claude/skills/`的符號連結。克勞德·科德立即掌握技能——無需重新啟動。



## 第 5 步：驗證



```
ls ~/.claude/skills/
```



您應該會看到已安裝的技能已符號連結。



## Claude 程式碼整合詳細資訊



- **技能路徑**：`~/.claude/skills/`（全域）或`.claude/skills/`（專案）
- **CLAUDE.md**：技能共享技能使用 `SKILL.md` 格式，Claude Code 可以原生讀取該格式
- **專案模式**：在儲存庫中執行 `skillshare init -p` 來管理每個專案的 `.claude/skills/`



## 下一步是什麼？號



- [管理多種技巧→](https://skillshare.runkids.cc/docs/how-to/daily-tasks/organizing-skills)
- [與你的團隊分享→](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing)
- [探索更多技巧→](https://skillshare.runkids.cc/docs/reference/commands/search)