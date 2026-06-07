# Recipe: Cross-Machine Sync

> Source: https://skillshare.runkids.cc/docs/how-to/recipes/cross-machine-sync-recipe

---

# 配方：跨機同步



> 使用 git push/pull 在多台機器上保持技能同步。



## 場景



您在桌上型電腦和筆記型電腦（或家庭和辦公室機器）上工作。您希望在任何地方都可以使用相同的技能庫，而無需在每台電腦上重新執行安裝命令。



## 解決方案



### 初始設定（機器 A）



```
# Initialize skillshare
skillshare init
# Install your skills
skillshare install your-org/team-skills
skillshare install another/repo --into tools
# Push source to a git remote
skillshare push
```



`skillshare push` 將來源目錄提交至 git-tracked 分支並推送到配置的遠端。



### 在新機器（機器 B）上進行設定



```
# Install skillshare
curl -fsSL https://raw.githubusercontent.com/runkids/skillshare/main/install.sh | sh
# Initialize
skillshare init
# Pull from remote
skillshare pull
# Sync to local targets
skillshare sync
```



### 每日同步工作流程



在任何機器上：



```
# Pull latest changes from other machines
skillshare pull
# Sync to local AI tools
skillshare sync
# After making changes locally
skillshare push
```



## 驗證



- `skillshare push` 退出 0 並報告已提交的更改
- 另一台機器上的`skillshare pull`顯示收到的更改
- `skillshare list` 在兩台機器上表現出相同的技能
- `skillshare sync` 在目標機器上建立符號鏈接



## 變化



- **登入時自動同步**：將 `skillshare pull && skillshare sync` 新增至您的 shell 設定檔 (`.bashrc` / `.zshrc`)
- **衝突解決**：如果兩台機器修改相同的技能，`pull`使用git merge－解決來源目錄中的衝突
- **選擇性同步**：使用`config.yaml`中的每個目標`include` / `exclude`過濾器來控制哪些技能同步到每台機器



## 相關



- [跨機同步指南](https://skillshare.runkids.cc/docs/how-to/sharing/cross-machine-sync)
- [`push`指令參考](https://skillshare.runkids.cc/docs/reference/commands/push)
- [`pull`指令參考](https://skillshare.runkids.cc/docs/reference/commands/pull)