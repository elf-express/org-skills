# AI-Assisted Development

> Source: https://skillshare.runkids.cc/docs/learn/with-ai-coding-agents

---

# AI輔助開發



> 使用人工智慧編碼代理透過預先建立的專案技能來促進技能共享。



## 先決條件



- AI編碼代理（Claude Code、Codex等）
- 本地克隆的技能共享儲存庫



## 設定



該倉庫在 `.skillshare/skills/` 中提供了專案模式技能。將它們同步到您的代理：



```
skillshare sync -p
```



您的 AI 代理現在可以使用處理此程式碼庫的專業技能。



## 可用技能



|技能|它有什麼作用 |
| --- | --- |
|實作功能 |使用 TDD 工作流程從規格文件或描述中實現功能 |
|更新文檔 |更新網站文檔以匹配最近的程式碼更改，根據原始程式碼交叉驗證每個標誌 |
|程式碼庫審計 |交叉驗證 CLI 標誌、文件、測試和目標，以確保整個程式碼庫的一致性 |
| cli-e2e-測試 |從 Runbook 在 devcontainer 中執行隔離的 E2E 測試 |
|變更日誌 |以常規格式從最近的提交產生 CHANGELOG.md 條目 |



## 典型工作流程



1. **啟動一項功能** — 要求您的代理程式使用 `implement-feature` 和規範
2. **更新文件** — 程式碼變更後，呼叫`update-docs`同步網站文檔
3. **審核一致性** — 運行 `codebase-audit` 捕獲標誌/文檔不匹配
4. **執行 E2E 測試** — 使用 `cli-e2e-test` 在沙箱中進行驗證
5. **編寫變更日誌** — 在發布之前呼叫 `changelog`



## 下一步是什麼？號



- [開發容器設定→](https://skillshare.runkids.cc/docs/learn/with-devcontainer)
- [互動遊樂場→](https://skillshare.runkids.cc/docs/learn/with-playground)
- [貢獻指南→](https://github.com/runkids/skillshare/blob/main/CONTRIBUTING.md)