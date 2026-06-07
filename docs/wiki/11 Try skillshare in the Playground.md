# Try skillshare in the Playground

> Source: https://skillshare.runkids.cc/docs/learn/with-playground

---

# 在 Playground 中嘗試技能分享



> 預先配置的 Docker 沙箱，包含演示技能、審核規則和項目 - 幾秒鐘內即可探索。



## 先決條件



- 安裝了 Docker 和 Docker Compose
- 克隆技能共享儲存庫：`git clone https://github.com/runkids/skillshare.git`



## 啟動遊樂場



```
cd skillsharemake playground
```



這個命令：



1. 建置沙箱 Docker 映像（包含 Go 工具鏈）
2. 在容器內編譯`skillshare`二進位文件
3. 初始化全域模式，自動偵測所有目標
4. 創建跨類別的簡報技能（乾淨、警告、關鍵）
5. 設定具有項目級技能和自訂審核規則的簡報項目
6. 將您帶入互動式 shell — 準備探索



## 裡面有什麼



### 演示技能（全球）



|技能|類別 |審計結果|
| --- | --- | --- |
|審核示範清理 |根 |無（乾淨基線）|
|部署清單 |開發營運/ |無 |
|審核演示 ci 發布 |安全/ |高 + 中（sudo、外部 URL）|
|審核演示調試 exfil |安全/ |嚴重（憑證洩露）|
|審核示範外部連結 |安全性/ |低（外部 URL）|
|審核演示懸空連結 |安全/ |低（本地連結損壞）|



### 示範專案 (`~/demo-project`)



預先配置的 `.skillshare/` 項目包含：



- `hello-world` — 乾淨的專案技能
- `demos/audit-demo-release` — 發布有審核警告的助手
- `guides/code-review` — 嵌套程式碼審查指南
- 自訂`audit-rules.yaml`，帶有 TODO/FIXME 策略規則



### 自訂審核規則



全域和專案等級 `audit-rules.yaml` 均已預先配置，因此您可以了解規則自訂的工作原理 - 啟用/停用規則、新增自訂模式、設定白名單。



## 值得嘗試的事情



```
# Check what's installed
skillshare status
skillshare list
# Run a security audit — see findings across severity levels
skillshare audit
# Try project mode
cd ~/demo-project
skillshare status          # auto-detects project mode
skillshare audit           # project-level scan with custom rules
# Launch the web dashboard (port 19420)
skillshare-ui              # global mode
skillshare-ui-p            # project mode
# Explore nested skills
ls ~/.config/skillshare/skills/security/
ls ~/.config/skillshare/skills/devops/
```



## 僅模式



從頭開始－沒有自動初始化，沒有簡報內容：



```
./scripts/sandbox_playground_up.sh --bare./scripts/sandbox_playground_shell.sh
```



對於從頭開始測試`skillshare init`很有用。



## 停止遊樂場



```
make playground-down
```



資料保留在 Docker 磁碟區中 (`playground-home`)。接下來`make playground` 從上次停下的地方繼續。



## 架構



Playground 在具有安全強化功能的**只讀** Docker 容器中運行：



- `read_only: true` — 除指定磁碟區外，檔案系統是不可變的
- `cap_drop: ALL` — 沒有 Linux 功能
- `no-new-privileges` — 防止權限升級
- 可寫卷：`/sandbox-home`（持久）、`/tmp`（tmpfs，256 MB）
- 連接埠 `19420` 轉送至 Web 儀表板



工作區從主機儲存庫以唯讀方式安裝 - 您可以在電腦上編輯程式碼並在容器內重建。



## 下一步是什麼？號



- [入門→](https://skillshare.runkids.cc/docs/getting-started)
- [安全審核指南→](https://skillshare.runkids.cc/docs/how-to/advanced/security)
- [Docker 沙箱指南→](https://skillshare.runkids.cc/docs/how-to/advanced/docker-sandbox)