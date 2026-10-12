# 模型選擇與 RD 派工

核對日期：2026-10-12。以下是本模板的建議起始配置，不是跨模型效能實測或等價保證。GPT 的 API 型號、Codex 帳號可選型號、ChatGPT Work 可派工型號可能不同；以當前 runtime 顯示的可用清單為準。Claude alias 會隨平台更新，不把某個版本永久寫死。

## 角色與起始配置

| 角色 | 任務界線 | Claude Code 預設 | GPT / Codex 預設 | GPT effort |
|---|---|---|---|---|
| PM（主對話） | 需求、取捨、派工、驗收；不寫產品程式碼 | Opus | GPT-6 Astra（gpt-6-astra） | medium |
| rd_architect | 唯讀技術分析、資料流與介面拆解；不改碼 | opus | gpt-6-astra | medium |
| rd | 一般功能、整合、自測；只寫明確分配的測試 | sonnet | gpt-6.1-sol | medium |
| rd_mechanical | 已知位置、明確改法、可直接驗證的局部修改 | haiku | gpt-6-luna | high |
| rd_debugger | 根因不明、複雜狀態、跨模組除錯 | opus | gpt-6-astra | medium |
| test_writer | 依規格設計、撰寫測試；不兼任最終驗收 | sonnet | gpt-6.1-sol | high |
| reviewer | 規格符合度、品質、測試缺口；唯讀、不跑測試 | sonnet | gpt-6.1-sol | high |
| tester | 驗收推理、實際測試、回歸；不修改來源檔 | sonnet | gpt-6.1-sol | medium |

PM 主對話模型由使用者在工具中選擇，AGENTS.md 不會替使用者切模型。其餘七個角色均提供兩平台的明確模型設定；Claude 的 effort 本模板不固定，使用當前模型支援的設定，必要時派工記錄實際值。不要把 GPT effort 或 ID 直接抄到 Claude 設定。

## 怎麼選 RD

1. 使用者已要求研究或規劃，但技術方案、資料關係或相容性不清楚：先用 **rd_architect** 只讀分析；PM 把分析納入既有範圍，新增產品取捨仍交使用者決定。
2. 檔案與改法已明確，沒有架構或業務規則判斷：用 **rd_mechanical**。不是單檔案就一定簡單；權限、帳務、交易、資料刪除與併發不得以檔案少為由降級。
3. 一般功能與多檔案整合：用 **rd**。預設選這個，不為每個任務額外增加分析角色。
4. 根因不明或一般 RD 已有證據顯示推理卡住：用 **rd_debugger**；提供重現方式、log、已試方法與結果。

角色是可選的工作種類，不是四個強制階段。一次只啟動必要角色；同型小改可合併一張派工單。

例：調整已定位的 Header 標籤 → rd_mechanical；新增有既定介面的代點功能 → rd；設計代點權限與訂單資料流 → rd_architect；代點後跨帳單狀態偶發錯誤 → rd_debugger。

## Test Writer / Reviewer / Tester

- 三者均以 Sonnet / Sol 起步。Test Writer 需推導案例及預期，Sol 預設 high；Tester 負責獨立驗收及執行，Sol 預設 medium。獨立性來自新上下文、規格與證據，不要求兩者使用不同模型。
- PM 在 RD 及 Test Writer 完成、整合且 Reviewer 通過後另開全新的 Tester，不續用 Test Writer 對話。
- 權限、帳務、併發、資料遷移或跨模組高風險變更，Reviewer 升至 Opus / Astra；Test Writer / Tester 有跨規則推理需求或實際漏測證據時也可升級；PM 在派工與 PR 記錄理由。
- Tester 不全部降到 Haiku / Luna：它需要判斷業務驗收與測試缺口。只有跑已指定指令及整理輸出可交腳本／CI；這不能代替獨立驗收。
- 純文件及設定變更只需語法、引用、差異與規則一致性檢查；不虛構應用程式測試。

## 升級、失敗與上限

先分類，再處理；最多退回兩次的上限包含 Reviewer 與 Tester 退回，模型升級不重置計數。

| 原因 | 處理 |
|---|---|
| 缺少背景 | 補相關檔案、介面及規格，優先續接原 RD |
| 推理／整合卡住 | Haiku → Sonnet → Opus；Luna → Sol → Astra，或拆小任務；已在最高建議層就重切任務，不無限加大模型 |
| 技術根因不明 | 交 rd_debugger，攜帶已嘗試方法與證據 |
| 需求矛盾／新增產品行為 | PM 向使用者釐清，不由模型自行擴張範圍 |
| 環境、權限、依賴故障 | 回報具體阻礙，先處理環境，不以換大模型重複撞牆 |
| 模型不可用 | 不靜默改模型；選同平台可用候選，記錄實際型號及能力／用量差異，無合適候選就回報 |

升級後驗收標準不變。禁止「為了省 token」略過安全或必要驗證。

## 設定在哪裡、怎麼生效

- **共用規則**：AGENTS.md；角色行為：docs/agents/。這些文件本身不是模型路由程式。
- **Claude Code**：`.claude/agents/*.md` 的 `model` 是預設；目前官方文件允許單次派工指定模型，但環境強制覆寫等設定可能改變結果。啟動後查實際執行模型；不要只憑角色名稱宣稱模型已切換。
- **Codex 本機**：`.codex/agents/*.toml` 的 `model` 與 `model_reasoning_effort` 是預設。官方文件說自訂 agent 檔中的值優先於一般 spawn 設定；要升級時使用另設的角色配置，或使用 generic worker 明確指定模型／effort，並在派工中要求讀取同一份角色文件。不能只改文字 prompt 就宣稱已升級。
- **ChatGPT Work 或未載入專案 agent 設定的環境**：由 PM 用實際提供的 subagent 工具明確帶入模型與 effort、共用規則及角色文件路徑；不假設 `.codex/agents` 自動生效。runtime 不允許換模型就如實回報。
- **跨供應商**：這是兩套可選配置，不是 Claude Code 原生同時呼叫 GPT 的橋接器。沒有已配置的跨平台工具時，不承諾在單一 session 自動混用兩家模型。
- **權限**：角色限制和工具白名單不等於 OS 隔離。Claude 的 Bash 仍可能寫檔；Codex sandbox 也可能受主 session 設定覆寫。不得利用這些能力繞過 Reviewer 唯讀或 Tester 不改來源檔的規則。

## 派工紀錄

**由 PM 在本次規劃書內維護**，格式見 docs/plans/README.md；派工前建檔、回報與升級時更新，不等 RD 完工才補。各角色提交證據，PM 記錄：角色、平台、實際模型、effort（或未指定／不支援）、選模理由、輸入規格、允許檔案、測試方式、退回次數。結果記錄實際指令、耗時與 token／成本（工具提供才記，不估造數字）、缺陷與漏測。用數個真實任務比較總成本與品質，不能只比較每 token 單價。

## 官方參考

- [OpenAI 模型目錄](https://developers.openai.com/api/docs/models)
- [Codex / ChatGPT subagents 與自訂角色設定](https://learn.chatgpt.com/docs/agent-configuration/subagents)
- [Codex AGENTS.md](https://learn.chatgpt.com/docs/agent-configuration/agents-md)
- [Claude Code 自訂 subagents](https://code.claude.com/docs/en/sub-agents)
- [Claude Code AGENTS.md 與 CLAUDE.md 載入](https://code.claude.com/docs/en/memory)
