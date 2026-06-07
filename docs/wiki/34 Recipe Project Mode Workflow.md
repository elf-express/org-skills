# Recipe Project Mode Workflow

> Source: https://skillshare.runkids.cc/docs/how-to/recipes/skill-per-project-workflow

---

# 配方：專案模式工作流程



> 管理與您的程式碼庫相伴的專案範圍技能。



## 場景



您希望將特定技能投入到您的專案儲存庫中，以便：



- 每個貢獻者都會得到相同的人工智慧指令
- 技能與程式碼一起進行版本控制
- 除了克隆存儲庫之外無需手動設置



## 解決方案



### 第 1 步：初始化專案模式



```
cd your-project
skillshare init -p
```



這將在您的專案根目錄中建立`.skillshare/config.yaml`。



### 第 2 步：安裝專案範圍的技能



```
skillshare install anthropics/courses/prompt-eng -p
skillshare install your-org/team-skills --skill code-review -p
```



技能放在`.skillshare/skills/`。



### 第 3 步：同步到專案目標



```
skillshare sync -p
```



這會建立從 `.skillshare/skills/` 到專案級目標目錄的符號連結（例如，`.claude/skills/`、`.cursor/skills/`）。



### 第 4 步：致力於版本控制



```
git add .skillshare/
git commit -m "Add project skills"
```



### 第 5 步：隊友設定



當隊友克隆儲存庫：



```
git clone your-org/your-project
cd your-project
skillshare sync -p
```



一個指令即可將所有專案技能同步到本地人工智慧工具。



## 驗證



- `.skillshare/config.yaml`存在於專案根目錄中
- `.skillshare/skills/`包含已安裝的技能
- `skillshare list -p`展示專案技巧
- `sync -p`之後，目標目錄包含符號鏈接



## 變化



- **開發容器自動同步**：將 `skillshare sync -p` 加入 `.devcontainer/devcontainer.json` `postCreateCommand`
- **混合模式**：根據個人喜好使用全局技能+根據團隊標準使用專案技能
- **CI驗證**：將`skillshare audit -p`新增至CI管道以驗證專案技能



## 相關



- [專案設定指南](https://skillshare.runkids.cc/docs/how-to/sharing/project-setup)
- [了解專案技巧](https://skillshare.runkids.cc/docs/understand/project-skills)
- [開發容器指南](https://skillshare.runkids.cc/docs/learn/with-devcontainer)