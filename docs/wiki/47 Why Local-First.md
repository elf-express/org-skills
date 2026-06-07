# Why Local-First

> Source: https://skillshare.runkids.cc/docs/understand/philosophy/why-local-first

---

# 為什麼本地優先



> Skillshare 是一個沒有運行時依賴性的單一二進位。原因如下。



## 決定



Skillshare 作為單一 Go 二進位檔案發布。沒有 Node.js，沒有 Python，沒有套件管理器，沒有守護程式。安裝它，運行它，完成。



這不是阻力最小的道路，而是由三個原則驅動的深思熟慮的選擇。



## 原則 1：零依賴鏈



每個依賴項都是攻擊面和維護負擔。



如果 Skillshare 需要 Node.js，您需要管理 Node 版本、處理 `node_modules`、處理特定於平台的本機模組，並信任整個 npm 供應鏈。對於管理人工智慧技能的工具（它們本身就是不可信的內容）來說，添加不可信的依賴鍊是不可接受的。



Go 編譯為靜態二進位。依賴鏈在編譯時結束。你下載什麼，你就運行什麼。



## 原則 2：在任何地方都以相同的方式運作



技能共享運行於：



- macOS（英特爾和蘋果晶片）
- Linux（amd64 和 arm64）
- Windows (amd64)
- Docker 容器（無需特殊設定）
- CI/CD 管道（無需語言執行時間）
- 開發容器和程式碼空間



單一二進位檔案意味著所有平台上的行為相同。沒有“在我的機器上運行”調試。無 CI 環境漂移。



## 原則 3：預設離線



Skillshare 的核心操作 — `sync`、`list`、`status`、`backup`、`restore` — 無需網路存取即可運作。只有明確需要遙控器的操作（`install`、`search`、`check`、`update`、`push`、`pull`）才需要連接。



這對於：



- **氣隙環境**：國防、醫療保健和金融機構經常限制網路訪問
- **不可靠的連線**：火車、飛機、會議 WiFi
- **速度**：本地操作在毫秒內完成，而不是秒



## 為什麼不使用套件管理器外掛？號



我們考慮以 npm 套件、Homebrew 公式（我們現在支援作為附加管道）或 pip 套件的形式進行運輸。每個都有同樣的問題：它們添加了 Skillshare 的用戶可能沒有或不想要的運行時依賴項。



在 Windows 上使用 Cursor 的開發人員不需要安裝 Homebrew。運行 Alpine Linux 的 CI 管道不需要 Node.js。該工具應該適應用戶的環境，而不是相反。



## 權衡



單二進位方法的成本是：



- **建構複雜性**：針對 6 個以上目標的交叉編譯，停用 CGO
- **更新機制**：無`npm update` — Skillshare 有自己的`upgrade` 指令
- **UI 交付**：Web 儀表板無法與二進位檔案捆綁（太大），因此在運行時下載並快取



我們接受這些權衡，因為它們使用戶體驗保持簡單：下載、運行、完成。



## 相關



- [安全第一的設計](https://skillshare.runkids.cc/docs/understand/philosophy/security-first)
- [與其他工具比較](https://skillshare.runkids.cc/docs/understand/philosophy/comparison)