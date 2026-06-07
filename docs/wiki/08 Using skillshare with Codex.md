# Using skillshare with Codex

> Source: https://skillshare.runkids.cc/docs/learn/with-codex

---

# 將技能共享與 Codex 結合使用



> 從安裝到第一次同步 — 5 分鐘。



## 先決條件



- [OpenAI Codex CLI](https://github.com/openai/codex)已安裝並運行
- macOS、Linux 或 Windows



## 第 1 步：安裝技能共用



```
curl -fsSL https://raw.githubusercontent.com/runkids/skillshare/main/install.sh | sh
```



## 第 2 步：初始化



```
skillshare init
```



這會偵測 Codex 的技能目錄 (`~/.codex/skills/`) 並自動將其新增為目標。



## 第 3 步：安裝你的第一個技能



```
skillshare install runkids/my-skills
```



## 步驟 4：同步



```
skillshare sync
```



技能符號連結到`~/.codex/skills/`。



## 第 5 步：驗證



```
ls ~/.codex/skills/
```



您應該會看到已安裝的技能已符號連結。



## 法典具體註釋



- **技能路徑**：`~/.codex/skills/`（全域）或`.agents/skills/`（專案）
- **描述限制**：Codex 對技能描述有 1024 個字元的限制。保持 `SKILL.md` frontmatter 中的 `description` 字段簡潔
- **專案模式**：運行`skillshare init -p`來管理專案級Codex技能



## 下一步是什麼？號



- [管理多種技巧→](https://skillshare.runkids.cc/docs/how-to/daily-tasks/organizing-skills)
- [與你的團隊分享→](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing)
- [探索更多技巧→](https://skillshare.runkids.cc/docs/reference/commands/search)