# Recipe Team Onboarding

> Source: https://skillshare.runkids.cc/docs/how-to/recipes/team-onboarding-recipe

---

# 秘訣：團隊入職



> 在 5 分鐘內設定新團隊成員的 AI 技能環境。



## 場景



新的開發人員加入您的團隊。他們需要：



- 組織範圍內的技能（編碼標準、審查指南）
- 專案特定技能（領域知識、架構規則）
- 他們的人工智慧工具（Claude Code、Cursor 等）中的所有內容均有效



## 解決方案



### 第 1 步：建立入門腳本



在您的團隊 wiki 或儲存庫中另存為 `scripts/setup-skills.sh`：



```
#!/bin/bash
set -e
echo "Installing skillshare..."
curl -fsSL https://raw.githubusercontent.com/runkids/skillshare/main/install.sh | sh
echo "Initializing..."
skillshare init
echo "Installing organization skills..."
skillshare install your-org/org-skills
echo "Running security audit..."
skillshare audit
echo "Syncing to all AI tools..."
skillshare sync
echo "Done! Run 'skillshare list' to see installed skills."
```



### 第 2 步：新員工執行腳本



```
curl -fsSL https://your-org.github.io/setup-skills.sh | sh
```



或者，如果腳本位於團隊儲存庫中：



```
git clone your-org/team-tools
./team-tools/scripts/setup-skills.sh
```



### 第 3 步：特定於項目的設定



當新員工複製一個專案時：



```
cd your-project
skillshare sync -p
```



這會自動獲取專案範圍的技能。



### 第 4 步：驗證一切正常



```
# Check global skills
skillshare list
# Check project skills
skillshare list -p
# Check sync status
skillshare status
```



## 驗證



- `skillshare list`展現組織能力
- `skillshare status` 顯示所有目標已同步
- 開啟克勞德代碼/遊標顯示技能已加載



## 變化



- **開發容器入門**：如果您的團隊使用開發容器，請將技能共享新增至`.devcontainer/Dockerfile`和`postCreateCommand` - 容器啟動時技能已準備就緒
- **基於自製程式的安裝**：對於 macOS/Linux 團隊，將 `curl | sh` 替換為 `brew install skillshare`
- **中心發現**：將新員工引導至您的中心：`skillshare search --hub https://your-org.github.io/skillshare-hub.json`



## 相關



- [入門指南](https://skillshare.runkids.cc/docs/getting-started)
- [組織分享](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing)
- [項目設定](https://skillshare.runkids.cc/docs/how-to/sharing/project-setup)
- [開發容器指南](https://skillshare.runkids.cc/docs/learn/with-devcontainer)