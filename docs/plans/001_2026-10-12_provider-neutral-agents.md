# 001 — 平台中立協作規則與 RD 模型分工

## 已確認需求

使用者要求模板不限定 Claude，將 CLAUDE 共用內容移至 AGENTS，細分 RD 並提供 GPT 與 Claude 的模型建議。先前討論建議保留 PM／RD／Reviewer／Tester，依難度與風險選模型。

## 範圍

- 共用八階段流程及原 PR #3 的範圍／一次性處理規則合入 AGENTS.md。
- CLAUDE.md 僅留相容 import；角色內容集中 docs/agents/，平台設定引用它。
- RD 分為分析、一般實作、機械修改、除錯；Reviewer／Tester 保留獨立角色。
- 提供 Claude 與 Codex 六個 agent 的明確模型配置，說明模型升級與 runtime 限制。
- README 改為 AI PM Workflow，修正舊流程；GitHub 新名稱 ai-pm-workflow 為建議，需可用管理操作才可執行。
- 不修改 OrderManagementSystem、不執行資料操作、不新增跨供應商路由服務、不合併 PR。

## 驗收

1. 原有 PM 八階段、使用者確認／merge、最多退回兩次、一次性處理規則仍在。
2. AGENTS.md 明確區分 PM 與 subagent；README、角色、流程引用一致。
3. 兩平台各六個角色，模型及 GPT effort 明確，寫入與唯讀角色界線一致。
4. 模型建議同時包含 GPT 和 Claude，不混用兩平台 ID，不宣稱設定已實際執行。
5. SYSTEM_STATUS 在開 PR 前記錄改版與待合併狀態；GitHub repository 名稱是否更改如實回報。

## 實作順序與驗證

搬移共用流程 → 整理角色文件與平台設定 → 補模型選擇與 README → 檢查語法／引用／原規則保留 → 獨立審查 → 獨立設定驗證 → 提交並開 PR。

主要風險：兩份規則漂移（只留單一來源）；角色誤讀 PM 禁令（明確作用範圍）；無法使用的模型／不同客戶端設定（核對官方資料及 runtime 可用性）；PR #3 成果遺失（分支接續其 commit）。

狀態：✅ 實作與驗證完成，待合併。獨立 Reviewer 初審兩項衝突已修正，獨立複核通過；Tester 兩平台語法、引用、模型映射與原規則保留檢查通過。本環境無 Claude Code／Codex CLI，尚未驗證真實載入與模型執行。遠端更名另受 GitHub 瀏覽器登入阻擋。


2026-10-12 於任務 002 查證：PR #4 已合併，repository 已更名為 ai-pm-workflow；上段為交付當時的驗證及限制紀錄。後續流程調整見規劃書 002 與 D002。
