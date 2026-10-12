# 系統現況與功能清單

最後更新：2026-10-12。本工作分支反映模板改版；已合併現況以 main 為準。新專案複製時改填產品自己的資訊。

## 一、系統概觀

- 用途：AI PM Workflow，供 Codex / GPT 與 Claude Code 使用的協作文件模板。
- 技術：Markdown、Claude agent YAML frontmatter、Codex agent TOML；無應用程式。
- 共用入口：AGENTS.md；相容入口：CLAUDE.md；角色：docs/agents/；模型：docs/MODEL_ROUTING.md。
- 平台設定：.claude/agents/ 與 .codex/agents/，各七個角色（本分支新增 test_writer）。
- 驗證方式：Markdown 引用、YAML／TOML 語法、角色與模型對照、git diff --check；實际 CLI 載入需在安裝該工具的環境驗證。

## 二、功能清單

### F001 — 共用協作規則
- AGENTS.md 集中八階段、範圍及一次性處理規則；CLAUDE.md 只載入同一檔。
- 驗收：檢查原 PM 流程與使用者決策關卡保留，且 subagent 不套用 PM 禁寫程式的規則。

### F002 — RD 任務分工
- rd_architect 唯讀分析；rd 一般實作；rd_mechanical 明確局部修改；rd_debugger 疑難除錯。
- Test Writer 依規格撰寫指定測試；Reviewer 唯讀審查；PM 在整合及審查完成後另開全新 Tester 執行驗收，Tester 不能改來源檔。
- 驗收：兩平台角色及共用角色文件對應一致。

### F003 — GPT／Claude 模型配置
- 各角色明確指定模型；GPT 同時指定 effort。路由、升級、不可用模型與平台限制見 MODEL_ROUTING.md。
- 驗收：設定可解析、模型與角色表一致；不把靜態解析當成實際模型執行成功。

### F004 — 任務分類與 PM 紀錄
- 只有符合 TASK_SIZING 全部條件及案例才走小改，其餘一般功能。使用者確認後由 PM 增補案例。
- 唯讀分析可在實作核准前進行；PM 根據技術證據撰寫正式範圍與驗收，從派工前維護規劃書內的紀錄。
- 驗收：流程與角色文件一致，寫入權責與新 Tester 關卡明確。

## 三、進行中任務

- 規劃書：docs/plans/002_2026-10-12_task-sizing-test-roles.md。
- 階段：已通過獨立審查及靜態驗證，待合併；001 已於 PR #4 合併。
- 工作分支：docs/task-sizing-test-roles，從最新 main 開始。

## 四、已知限制

- 本環境沒有 Claude Code／Codex CLI，尚未做真實角色載入或付費模型呼叫驗證。
- GitHub repository 已更名為 terryincsc/ai-pm-workflow。
- Claude Bash 白名單與文字規則不是 OS 層唯讀保證；Codex 的 sandbox 可能受主 session 覆寫。

## 五、本次 002 驗證證據

- 獨立 Reviewer 初審發現小改案例例外矛盾，修正後由全新 Reviewer 複核 PASS；總退回 1 次。
- 全新 Tester：兩平台各七角色 YAML／TOML 解析、模型／effort／路徑映射、CLAUDE import、Markdown 連結與 fence、R1–R4 及原授權／PR 規則通過。
- git diff --check origin/main 通過；驗證前後 34 個 tracked 檔案雜湊相同，來源無變更。詳細版本與指令見規劃書 002。
- 未測實際 CLI 載入、模型執行或 OS 隔離。以下保留歷史證據。

## 六、前次 001 驗證證據

- 兩平台各六個 YAML／TOML 設定解析、角色路徑與模型／effort 對照通過。
- Markdown 引用與 fence、CLAUDE 單行 import、八階段及原範圍規則保留檢查通過。
- git diff --check 通過。初審指出分析角色派工時機及唯讀義務衝突，已修正並通過獨立複核。
- Tester 驗證前後 29 個檔案雜湊不變；收尾狀態由 PM 更新。未測 CLI 載入與實際模型執行。
