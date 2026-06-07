# Centralized vs Local

> Source: https://skillshare.runkids.cc/docs/how-to/advanced/local-first

---

# 集中式與本地優先



技能管理工具通常遵循兩種架構方法之一：**集中式平台**或**本地優先**。兩者都不是普遍更好的——每一種都需要權衡。本頁將介紹這兩種方法，以便您可以決定哪一種適合您的工作流程。

這不是功能比較

有關功能等級的差異（安裝流程、設定格式等），請參閱[比較技能管理方法](https://skillshare.runkids.cc/docs/understand/philosophy/comparison)。本頁重點在於**架構權衡** - 您的資料所在的位置、發現的工作方式以及您控制的內容。



## 兩種方法



### 集中式平台



集中式平台託管統一的註冊表，技能可在其中發布、搜尋和排名。安裝活動被匯總到社群指標中，例如下載計數和趨勢排名。



**優勢**：



- 內建發現 — 在一個地方瀏覽、搜尋和比較技能
- 社群訊號－下載計數和趨勢有助於展現流行技能
- 低摩擦－發現時無需設定；只需搜尋並安裝



**注意事項**：



- 安裝活動由平台追蹤
- 排名和計數規則由平台營運商管理



### 本地優先（技能共享）



Skillshare 保留您電腦上的所有狀態。技能透過`git clone`安裝並透過本機設定檔進行管理。沒有任何內容發送到遠端伺服器。



**優勢**：



- 零遙測－無安裝跟踪，無數據發送到任何地方
- 完全所有權－你的技能存在於你自己的檔案系統中
- 初始安裝後離線工作
- 單一二進位文件，無運行時依賴性



**注意事項**：



- 沒有內建的社群指標（下載計數、趨勢）
- 發現需要設定或連接到集線器



## 發現



本地優先並不意味著沒有發現。 Skillshare 提供三種發現管道：



|頻道|它是如何運作的 |
| ---| ---|
| GitHub 搜尋 | Skillshare search <query> — 直接搜尋公用 GitHub 儲存庫 |
|公共樞紐 | Skillshare search --hub — 查詢內建社區中心 |
|客製化中心 | Skillshare search --hub <url> — 查詢您或您的組織維護的任何中心 |



### 什麼是集線器？號



中心是一個靜態 JSON 檔案 (`skillshare-hub.json`)，其中列出了技能及其名稱、描述、來源和標籤。它可以存在於任何地方——Git 儲存庫、HTTP 伺服器或本機檔案系統：



```
# Build an index from your installed skills
skillshare hub index
# Search an organization's internal hub
skillshare search --hub https://internal.corp/skills/hub.json
# Search a local index file
skillshare search --hub ./skillshare-hub.json
```



集線器是獨立的－任何人都可以建立集線器，並且使用者可以同時連接到多個集線器。這使得它們非常適合需要與公共技能目錄一起維護私人技能目錄的組織。



詳細演練請參考【中心索引指南】(https://skillshare.runkids.cc/docs/how-to/sharing/hub-index)。



### 自託管指標



Skillshare 本身不會追蹤安裝，但如果您在自己的伺服器上託管集線器，則可以添加對您有意義的任何分析層：



1. 在您的伺服器上託管`skillshare-hub.json`
2. 新增請求日誌記錄或輕量級分析端點
3. 追蹤搜尋點擊、安裝推薦或您關心的任何指標



這使得技能作者或組織可以根據自己的條件來衡量採用情況。



## 選擇正確的方法



**如果符合以下條件，集中式平台可能更適合：**



- 您需要開箱即用的內建社群指標和趨勢
- 您喜歡單一瀏覽目的地來發現技能
- 您只需使用一個 AI CLI，無需跨工具同步



**本地優先可能更適合，如果：**



- 您使用多個AI CLI並希望統一管理
- 您希望安裝活動保留在您的電腦上
- 您需要離線操作或在受限網路環境中工作
- 您是一個需要控制哪些技能可用且可發現的組織



---



## 另請參閱



- [比較技能管理方法](https://skillshare.runkids.cc/docs/understand/philosophy/comparison) — 功能等級比較
- [中心索引指南](https://skillshare.runkids.cc/docs/how-to/sharing/hub-index) — 技能中心的建構與使用
- [集線器指令](https://skillshare.runkids.cc/docs/reference/commands/hub) — 集線器指令參考
- [安全指南](https://skillshare.runkids.cc/docs/how-to/advanced/security) — 技能安全掃描