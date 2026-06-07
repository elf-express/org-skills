# Recipe: Pre-commit Hook

> Source: https://skillshare.runkids.cc/docs/how-to/recipes/pre-commit-hook

---

# 配方：預提交鉤子



> 使用 [預先提交](https://pre-commit.com/) 框架在每次提交時自動運行 `skillshare audit`。



## 何時使用



在以下情況下，預提交掛鉤最有價值：



- **多重貢獻者編輯技能** - 團隊成員可能會無意中引入危險命令（`curl | bash`、`sudo rm -rf`）。鉤子在它們進入版本控制之前捕獲它們。
- **技能來自外部來源** - 從 GitHub、社群儲存庫或 AI 產生的內容複製技能會使手動審核變得困難。自動掃描提供了安全網。
- **您想要即時回饋** - CI 也會捕捉問題，但僅限於推送之後。該鉤子可以在幾秒鐘內為開發人員提供即時的本地回饋。



在以下情況下您可以跳過它：



- 你是唯一的作者並相信你所有的技能
- 技能很少改變（只有當`.skillshare/`或`skills/`檔案被修改時鉤子才會運行）



## 設定



新增到您專案的`.pre-commit-config.yaml`：



```
repos:  - repo: https://github.com/runkids/skillshare    rev: v0.16.8  # use latest release tag    hooks:      - id: skillshare-audit
```



然後安裝鉤子：



```
pre-commit install
```



## 工作原理



每當您提交與 `.skillshare/` 或 `skills/` 目錄相符的檔案的變更時，掛鉤就會執行 `skillshare audit -p`。如果任何發現超過配置的閾值，則提交將被阻止。



## 配置



該鉤子尊重您項目的 `.skillshare/config.yaml` 設定：



```
audit:  block_threshold: high  # block on HIGH+ findings
```



## 跳過鉤子



對於一次跳過：



```
SKIP=skillshare-audit git commit -m "your message"
```



## 要求



- `skillshare` CLI 必須安裝並在 `PATH` 中可用
- 專案必須使用`skillshare init -p`初始化



## 與 CI 結合



預提交掛鉤在本地捕獲問題，而 [CI/CD 驗證](https://skillshare.runkids.cc/docs/how-to/recipes/ci-cd-skill-validation) 為整個團隊提供安全網。將兩者用於縱深防禦。