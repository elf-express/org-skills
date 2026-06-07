# Using skillshare in Dev Containers

> Source: https://skillshare.runkids.cc/docs/learn/with-devcontainer

---

# 在開發容器中使用技能共享



> 在 VS Code 中打開，技能已準備就緒 — 無需本機安裝。



## 先決條件



- [VS 程式碼](https://code.visualstudio.com/) 與 [開發容器擴充](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)



## 工作原理



VS Code Dev Containers 讓您可以在 Docker 容器內進行開發。您在 `.devcontainer/` 中定義環境，VS Code 處理剩下的事情——打開項目，單擊“在容器中重新打開”，一切就準備好了。



Skillshare 自然地融入了這個工作流程。將其新增至`postCreateCommand`，技能將在容器啟動時安裝並同步。



## 設定



增加兩件事到你的`.devcontainer/devcontainer.json`：



```
{
  "postCreateCommand": "curl -fsSL https://raw.githubusercontent.com/runkids/skillshare/main/install.sh | sh && skillshare init --no-copy --all-targets --no-skill && skillshare sync"
}
```



就是這樣。當團隊成員在 VS Code 中開啟專案並點擊「在容器中重新開啟」時：



1.skillshare自動安裝
2. `init` 非互動式運作 — 新增所有偵測到的 AI CLI 目標，跳過複製提示和內建技能安裝
3. `sync`向所有目標傳遞技能



## 新增專案技能



對於團隊共享技能，請將 `.skillshare/` 配置提交到儲存庫：



```
# Inside the container
skillshare init -p
skillshare install your-org/team-skills -p
```



然後提交並更新`postCreateCommand`以同步專案技能：



```
{
  "postCreateCommand": "curl -fsSL https://raw.githubusercontent.com/runkids/skillshare/main/install.sh | sh && skillshare init --no-copy --all-targets --no-skill && skillshare sync && skillshare sync -p"
}
```



現在，每個團隊成員在打開容器時都會獲得相同的技能。



## GitHub 程式碼空間



相同的 `.devcontainer/` 配置在 Codespaces 中運作，無需任何變更。 Codespaces 運行 `postCreateCommand` 的方式與 VS Code 相同。



## 使用 ssenv 進行隔離測試



在開發容器內，`ssenv` 允許您建立隔離的技能共享環境以進行平行測試。每個環境都有自己的 `HOME` 目錄，其中包含單獨的配置、技能和目標。



|指令 |它有什麼作用 |
| --- | --- |
| ssnew <姓名> |建立新的隔離環境 |
| ssuse <姓名> |切換環境 |
| ssback |回到原來的環境|
| SSL |列出所有環境 |
| ssrm <姓名> |刪除環境 |



```
ssnew demo && ssuse demo    # Create and switch
ss init && ss sync          # Commands run in isolation
ssback                      # Return to original
```



這對於測試配置變更或技能安裝非常有用，而不會影響您的主要設定。



## 下一步是什麼？號



- [專案技能設定→](https://skillshare.runkids.cc/docs/how-to/sharing/project-setup)
- [團隊分享→](https://skillshare.runkids.cc/docs/how-to/sharing/organization-sharing)
- [同步模式解釋→](https://skillshare.runkids.cc/docs/understand/philosophy/sync-modes-explained)