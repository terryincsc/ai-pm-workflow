# AI PM Workflow

供 **Codex / GPT 與 Claude Code** 使用的 PM 主導協作模板：PM 與使用者對齊需求，按任務指派合適的 RD，再由獨立 Reviewer 與 Tester 驗收，最後開一個 PR，由使用者合併。

共用流程全部維護在 [AGENTS.md](AGENTS.md)。`CLAUDE.md` 只保留 `@AGENTS.md` 相容入口，沒有第二份規則。各平台 agent 檔只保留模型、能力設定及角色文件引用。

> 文件名稱已採 AI PM Workflow；GitHub repository 目前仍叫 `claude-code-pm-workflow`。建議更名為 `ai-pm-workflow`，repository 設定更名尚未執行。不要把文件標題更新當成遠端網址已更改。

## 分工與模型

| 工作 | 角色 | Claude 建議 | GPT 建議 |
|---|---|---|---|
| 統籌與產品取捨 | PM 主對話 | Opus | GPT-6 Astra |
| 唯讀技術分析與拆解 | rd_architect | Opus | GPT-6 Astra |
| 一般功能與整合 | rd | Sonnet | GPT-6.1 Sol |
| 明確、可驗證的局部修改 | rd_mechanical | Haiku | GPT-6 Luna |
| 疑難除錯與根因修復 | rd_debugger | Opus | GPT-6 Astra |
| 規格及品質審查 | reviewer | Sonnet；高風險 Opus | GPT-6.1 Sol；高風險 Astra |
| 功能驗收與回歸 | tester | Sonnet | GPT-6.1 Sol |

只使用必要角色，不強制每項需求走完四種 RD。完整界線、effort、升級與可用性規則見 [模型選擇](docs/MODEL_ROUTING.md)。型號建議不是效能保證，也不是跨供應商自動路由。

## 檔案結構

| 路徑 | 用途 |
|---|---|
| AGENTS.md | 共用八階段流程、角色界線與授權規則 |
| CLAUDE.md | 一行相容載入入口 |
| docs/agents/ | 平台中立角色職責；寫入型 RD 共用 rd-common.md |
| .claude/agents/ | Claude Code 六個角色的 YAML frontmatter 與引用 |
| .codex/agents/ | Codex 六個角色的 TOML 設定與引用 |
| docs/MODEL_ROUTING.md | GPT／Claude 選模、升級與實際生效方式 |
| docs/SYSTEM_STATUS.md | 現況、功能、進行中任務；開 PR 前更新 |
| docs/DECISIONS.md | 已拍板決策與原因 |
| docs/BACKLOG.md | 待辦與點子 |
| docs/plans/ | 每次需求的規劃書 |

## 新專案使用

1. 複製 AGENTS.md、CLAUDE.md、docs/ 與需要的平台設定目錄到新專案。已有同名文件時先整合內容，不直接覆蓋。
2. 保留 docs/agents/、MODEL_ROUTING.md 與 docs/plans/README.md；將本模板的現況、決策、待辦及 001 規劃書改為新專案自己的內容，勿把模板維護紀錄當成產品需求。
3. PM 填入技術棧、啟動方式、測試指令，並確認平台實際支援哪些模型與 subagent 能力。
4. 需求討論 → 規劃書 → 使用者確認 → 同一工作分支落實文件與派工 → RD 自測 → Reviewer → Tester → 更新 SYSTEM_STATUS／開 PR → 使用者 merge。
5. 一需求一分支一 PR；不先開獨立 docs PR。未合併工作以該分支文件與 PR 為準，已合併現況以 main 為準。

### Claude Code

專案使用 `.claude/agents/*.md`。各角色已設定 model；一般 RD 保留可選的 `memory: project`。共用角色不依賴記憶功能。新版本可直接讀 AGENTS.md；一行 CLAUDE.md import 同時支援不能直接載入的環境。

### Codex / GPT

支援自訂 agent 的本機 Codex 使用 `.codex/agents/*.toml`，先確認帳號可用模型；AGENTS.md 提供共用規則。沒有這些型號時依 MODEL_ROUTING.md 選可用替代並記錄，不宣稱任意 GPT 介面都會自動載入設定。

ChatGPT Work 與其他 hosted runtime 不一定載入本機 Codex 角色檔；此時由 PM 透過可用工具指定角色文件、模型及 effort。沒有 subagent 工具須回報，不能假裝有獨立 Reviewer／Tester。

## 驗證與限制

本模板是文件與平台設定，不包含應用程式或模型橋接服務。檢查 Markdown 引用、TOML／YAML 語法、兩套角色映射及 `git diff --check`；真正的角色載入、模型執行與權限行為仍需在使用者的 Claude Code／Codex 安裝環境驗證。

保留 PR #3 的功能範圍與一次性處理規則。`docs/WORKFLOW_COMPARISON.md` 是 2026-09-19 的歷史比較，不當作目前其他專案的狀態。
