---
name: project_wiki_docs_to_mcp
description: "docs/wiki 是 skillshare 文件站的抓取+機翻快照,已編號 01–58,目標是做成 MCP;編修鐵則=不改原文"
metadata: 
  node_type: memory
  type: project
  originSessionId: 718cd9fa-4414-4bb4-8d1b-d9894a3bc119
---

`docs/wiki/` 是 skillshare 官方文件站 (skillshare.runkids.cc) 的抓取 + 機器翻譯快照(英文 H1 + `> Source:` 連結 + 繁中正文),共 58 個檔,2026-06-08 已重新編號為連續的 `NN 標題.md`(01–58,數字後加空格)。最終目的是**把這批文件做成 MCP 來源**(使用者語:「還需要編成MCP」)。

抓取造成的典型毀損:表格/流程圖被打散成零散單行、程式碼區塊多道指令擠成一行、標題在第一個 `-` 被截斷。已修:檔名/編號、6 個截斷 H1、41 檔的扁平化程式碼區塊、6 處流程圖(重建為 Mermaid)。**仍未處理**:部分 code fence 內被擠成一行的 ASCII 目錄樹/雙欄排版(如 22/41/44/33)。

**Why:** 之後若要建 MCP 或續修這批文件,需知道它的來源、結構與既有編修決策,否則容易誤改。

**How to apply:** 動這批文件時鐵則=**不改原文**(連機翻怪詞如「犯罪」=commit、「偏僻的」=remote 也保留),只還原結構;驗證用「去除所有空白後與備份逐字比對」找出非空白變更。相關:[[project_org_skills_purpose]]、[[feedback_consistency_first]]。
