# sdd-agent-team

> **一套基於 Claude Code 的 SDD（規格驅動開發）多代理系統**
> 輸入：產品想法 → 輸出：可交付的 MVP（程式碼 + 設計 + 驗證報告）
>
> **A SDD (Specification-Driven Development) multi-agent system built on Claude Code**
> Input: Product idea → Output: Shippable MVP (code + design + validation reports)

---

## 目錄 / Table of Contents

- [系統概覽 System Overview](#系統概覽-system-overview)
- [運作模式 How It Works](#運作模式-how-it-works)
- [Agent 名單 Agent Roster](#agent-名單-agent-roster)
- [目錄結構 Directory Structure](#目錄結構-directory-structure)
- [快速開始 Quick Start](#快速開始-quick-start)
- [斜線指令 Slash Commands](#斜線指令-slash-commands)
- [不可妥協原則 Non-Negotiable Principles](#不可妥協原則-non-negotiable-principles)
- [客製化指南 Customization Guide](#客製化指南-customization-guide)
- [授權 License](#授權-license)

---

## 系統概覽 System Overview

這個系統讓你用 Claude Code 運作一支虛擬的 MVP 開發團隊。
所有 agents 被嚴格限定在各自的職能範圍（Domain Lock），透過 PM 協調，依序通過 5 個 Phase Gate，最終交付可出貨的 MVP。

This system runs a virtual MVP development team inside Claude Code.
Every agent is strictly limited to its own domain (Domain Lock), coordinated by the PM through 5 sequential Phase Gates, ultimately delivering a shippable MVP.

```
Product Idea
     │
     ▼
  PM (Orchestrator)
     │
     ├── Phase 1: Discovery    → E1 Market Analyst + E2 Competitor Analyst + E3 Domain Consultant
     ├── Phase 2: Definition   → E4 Product Strategist
     ├── Phase 3: Design       → E5 Product Designer → E6 UI/UX Designer
     ├── Phase 4: Engineering  → E7 Frontend + E8 Backend + E13 AI Engineer (parallel)
     └── Phase 5: QA           → E10 QA Engineer
```

---

## 運作模式 How It Works

### SDD 循環 / SDD Cycle

每個任務都遵循相同的 5 步循環：
Every task follows the same 5-step cycle:

```
Spec → Assign → Execute → Validate → Next Task
```

| 步驟 Step | 執行者 Who | 說明 Description |
|-----------|-----------|-----------------|
| Spec | PM | 寫 Task Specification，定義 AC |
| Assign | PM | 指派給對應 agent |
| Execute | Employee Agent | 只輸出自己 domain 的內容 |
| Validate | PM | 逐條 AC 二元驗證（PASS / FAIL） |
| Next Task | PM | Gate 通過後開啟下一個 phase |

### Phase 流程 / Phase Flow

| Phase | 模式 Mode | Agents | 輸出 Output |
|-------|-----------|--------|-------------|
| 1 — Discovery | 依序串行 Sequential | E1 → E2 → E3 | MarketReport / CompetitorReport / DomainReport |
| 2 — Definition | 單一 Single | E4 | MVPDefinition JSON（must-have ≤ 8） |
| 3 — Design | 串行 Serial | E5 → E6 | DesignSpec → UISpec |
| 4 — Engineering | 並行 Parallel | E7 + E8 + E13 | Frontend + Backend + AI code |
| 5 — QA | 串行 Serial | E10 | TestPlan + BugReport + ship_recommendation |

---

## Agent 名單 Agent Roster

### 管理層 Management Layer

| Agent | Code | Level | 職責 Responsibility |
|-------|------|-------|---------------------|
| CEO | CEO | L0 | 策略決策、Go/No-Go、CLAUDE.md 更新 |
| PM | PM | L1 | SDD 協調、Task Spec、Validation、PRD |
| HR | HR | L1 | Agent 設計、Onboarding、System Prompt 撰寫 |

### 產品開發層 Product Engineering Layer（E1–E13）

| Agent | Code | Phase | 合法輸出 Output |
|-------|------|-------|-----------------|
| E1 Market Research Analyst | MA | 1 | `MarketReport` JSON |
| E2 Competitor Analyst | CA | 1 | `CompetitorReport` JSON |
| E3 Domain Consultant | DC | 1 | `DomainReport` JSON |
| E4 Product Strategist | PS | 2 | `MVPDefinition` JSON |
| E5 Product Designer | PD | 3 | `DesignSpec` JSON |
| E6 UI/UX Designer | UX | 3 | `UISpec` JSON |
| E7 Frontend Engineer | FE | 4 | Frontend code + Manifest + API contracts |
| E8 Backend Engineer | BE | 4 | Backend code + DB schema + API endpoints |
| E9 Operations Monitor | OPS | Cross | Ops Report + Phase gate alerts + Stale-state watchdog |
| E10 QA Engineer | QA | 5 | `TestPlan` + `BugReport` JSON |
| E11 DevOps/SRE Engineer | DEVOPS | Cross | `OpsInfraReport` JSON |
| E12 System Architect | ARCH | Cross | `ArchSpec` JSON |
| E13 AI Engineer | AIE | 4 | `AISpec` JSON（RAG / LLM / Vector DB / Prompt） |

---

## 目錄結構 Directory Structure

```
.claude/
├── agents/                      # 17 個 agent 定義文件
│   ├── ceo.md                   # L0：策略決策層
│   ├── pm.md                    # L1：SDD 協調層
│   ├── hr.md                    # L1：Agent 設計層
│   ├── e1-market-analyst.md     # Phase 1：市場研究
│   ├── e2-competitor-analyst.md # Phase 1：競品分析
│   ├── e3-domain-consultant.md  # Phase 1：領域可行性
│   ├── e4-product-strategist.md # Phase 2：產品策略
│   ├── e5-product-designer.md   # Phase 3a：產品設計
│   ├── e6-ui-ux-designer.md     # Phase 3b：UI/UX 設計
│   ├── e7-frontend-engineer.md  # Phase 4：前端工程
│   ├── e8-backend-engineer.md   # Phase 4：後端工程
│   ├── e9-ops-monitor.md        # Cross-phase：運維監控
│   ├── e10-qa-engineer.md       # Phase 5：品質保證
│   ├── e11-devops-sre.md        # Cross-phase：基礎設施
│   ├── e12-system-architect.md  # Cross-phase：系統架構
│   ├── e13-ai-engineer.md       # Phase 4：AI 工程
│   └── designer.md              # 選配：UI prototype 設計師
│
├── rules/                       # 全系統強制規則
│   ├── global.md                # 全域原則與輸出格式規範
│   ├── domain-lock.md           # Domain Lock 主表（每個 agent 的合法/禁止輸出）
│   ├── phase-gates.md           # 每個 Phase 的 Gate Checklist
│   └── security-baseline.md     # SEC-01 至 SEC-09 安全基線
│
├── skills/                      # PM 與各 agent 的可呼叫技能
│   ├── phase_gate_checker.md    # Phase Gate 核查
│   ├── security_checker.md      # 安全基線核查
│   ├── roster.md                # Agent 名冊查詢
│   ├── assign.md                # 任務指派
│   ├── watch.md                 # Session 監控
│   ├── phase.md                 # Phase 狀態查詢
│   ├── codex-executor.md        # 委派微任務給 OpenAI Codex
│   ├── gemini-executor.md       # 委派微任務給 Google Gemini
│   ├── ops-quick-commands.md    # E9 常用指令
│   ├── e4-skills/               # E4 專屬：MVP 策略方法論（9 個技能）
│   ├── e6-skills/               # E6 專屬：UI 設計模式（3 個技能）
│   ├── superpowers/             # E7/E8 專屬：brainstorm / write-plan / execute-plan
│   └── hr-onboarding.md         # HR Agent 新增流程
│
├── commands/                    # Slash 指令定義
│   ├── spec.md                  # /spec — 產出 Task Specification
│   ├── validate.md              # /validate — 驗收 Agent 輸出
│   ├── assign.md                # /assign — 指派任務
│   ├── phase-gate.md            # /phase-gate — 核查 Phase Gate
│   ├── security-check.md        # /security-check — 安全基線核查
│   ├── roster.md                # /roster — 查看 Agent 名冊
│   └── watch.md                 # /watch — 監控 Session 狀態
│
├── directives/                  # CEO 指令歷史（供參考）
│   └── CEO_DIRECTIVE_001.md
│
└── settings.json                # Hooks 設定（安全警示、Session 結束清單）

CLAUDE.md                        # 公司層級章規（系統頂層文件）
```

---

## 快速開始 Quick Start

### 1. 複製 .claude/ 目錄到你的專案

```bash
# 複製整個系統配置
cp -r /path/to/this-repo/.claude /your-project/.claude
cp /path/to/this-repo/CLAUDE.md /your-project/CLAUDE.md
```

### 2. 在 Claude Code 中開啟你的專案

```bash
cd /your-project
claude
```

### 3. 呼叫 PM 啟動系統

在 Claude Code 中輸入：
```
@pm 我想做一個 [產品想法]，幫我啟動 MVP 開發流程。
```

PM 會自動：
1. 建立 Phased Execution Plan
2. 依序產出 Phase 1 的 Task Specifications
3. 呼叫 E1、E2、E3 執行 Discovery

### 4. 使用斜線指令輔助工作流程

```bash
/spec P1 MA        # 為 E1 Market Analyst 產出 Task Spec
/validate P1-MA-001  # 驗收 E1 的輸出
/phase-gate        # 核查當前 Phase Gate 狀態
/roster            # 查看所有 agent 名冊
```

### 需求 Requirements

- [Claude Code](https://claude.ai/code) CLI（最新版）
- Claude API 存取權限（Sonnet 4.6 或以上建議）

---

## 斜線指令 Slash Commands

| 指令 Command | 說明 Description | 執行者 Executor |
|-------------|-----------------|-----------------|
| `/spec [phase] [agent_code]` | 產出 Task Specification | PM（依 agents/pm.md 內定義） |
| `/validate [task_id]` | 逐條 AC 驗收輸出 | PM（依 agents/pm.md 內定義） |
| `/assign [task_id] [agent]` | 指派任務給 agent | PM skill: assign |
| `/phase-gate` | 核查 Phase Gate 狀態 | PM skill: phase_gate_checker |
| `/security-check` | 執行安全基線 SEC-01~09 核查 | PM skill: security_checker |
| `/roster` | 查看所有 agent 名冊與職責 | PM skill: roster |
| `/watch` | 監控 Session 與 agent 狀態 | E9 OPS + PM skill: watch |

---

## 不可妥協原則 Non-Negotiable Principles

這 4 條原則在系統初始化時即生效，任何 agent 不得違反：

These 4 principles are active from system initialization. No agent may violate them:

### PRINCIPLE-01：Domain Lock 絕對執行
每個 agent 只能產出其被定義的 domain 內容。超出 domain 的任何輸出由 PM 立即拒絕並重新 assign。

**Every agent may only produce output within its defined domain. Any out-of-domain output is rejected by PM and re-assigned immediately.**

### PRINCIPLE-02：PM 永不產出內容
PM 的合法輸出只有三種：Task Specification、Validation Report、PRD update。

**PM's only legal outputs are: Task Specification, Validation Report, PRD update.**

### PRINCIPLE-03：Phase Gate 強制執行
Phase N+1 的任何 Task Spec 不得在 Phase N 所有任務 APPROVED 之前發出。

**No Phase N+1 Task Spec may be issued before all Phase N tasks are APPROVED.**

### PRINCIPLE-04：驗證是二元的
每條 AC 只有 PASS 或 FAIL，沒有「差不多通過」。

**Each Acceptance Criterion is PASS or FAIL. There is no partial pass.**

---

## 安全基線 Security Baseline

所有工程輸出（E7、E8、E13）必須通過以下 9 項安全基線（`rules/security-baseline.md`）：

All engineering output (E7, E8, E13) must pass these 9 security baseline checks:

```
[ ] SEC-01：無 hardcoded secrets / credentials / tokens（含 LLM API keys）
[ ] SEC-02：所有 API endpoints 有 input validation（malformed request 回 400）
[ ] SEC-03：SQL 使用 parameterized queries 或 ORM（禁止字串拼接）
[ ] SEC-04：Auth endpoints 有 rate limiting
[ ] SEC-05：CORS 限制為已知 origins（禁止 allow_origins=["*"]）
[ ] SEC-06：密碼使用 bcrypt（min cost 12）
[ ] SEC-07：Token 使用 HTTP-only cookies
[ ] SEC-08：AI input guardrails 已實作（防 prompt injection）— AI 功能適用
[ ] SEC-09：LLM API keys 透過環境變數注入
```

任何一項缺失 = 自動 FAIL，不論其他品質多高。
**Any single failure = automatic FAIL, regardless of overall output quality.**

---

## 客製化指南 Customization Guide

### 修改公司章規
編輯 `CLAUDE.md` — 此為公司層級頂層文件，定義 agent 名單、Phase 流程、核心原則。
Edit `CLAUDE.md` — this is the company-level master document defining agents, phases, and core principles.

### 修改個別 Agent
編輯 `.claude/agents/[agent-name].md` — 每個 agent 文件含有 YAML frontmatter（身份定義）和詳細系統提示。
Edit `.claude/agents/[agent-name].md` — each agent file contains YAML frontmatter (identity) and detailed system prompt.

### 新增 Agent
1. 在 `CLAUDE.md` 中定義新 agent 的 domain
2. 在 `.claude/rules/domain-lock.md` 中加入合法/禁止輸出表
3. 在 `.claude/agents/` 中建立新 agent 文件（參考 hr-onboarding.md 的格式）
4. 更新 `.claude/rules/phase-gates.md` 中對應 phase 的 gate checklist

### 移除不需要的 Agent
例如，若你的產品不含 AI 功能，可以移除 `e13-ai-engineer.md` 並在 Phase 4 Gate Checklist 中將 P4-AIE-001 標記為 NOT_APPLICABLE。

### 單一專案覆蓋（不修改公司章規）
在專案資料夾內建立獨立的 `CLAUDE.md`，繼承公司章規並加入專案特定設定：

For project-specific overrides without modifying the company-level rules, create a project-level `CLAUDE.md`:

```
your-project/
├── CLAUDE.md          ← 專案層級（繼承 + 覆蓋）
└── .claude/           ← 公司層級（從此 repo 複製）
```

### settings.json Hooks
`.claude/settings.json` 內建兩個 Hook，開箱即用：
- **PreToolUse（Bash）**：偵測潛在 secret 操作，觸發安全警告
- **Stop**：Session 結束時顯示 Checklist（Phase Gate / PRD / Agent 狀態）

> **注意 Note：** 請勿將 `.claude/settings.local.json` 提交到版本控制，此檔案含有本地路徑設定。
> Do not commit `.claude/settings.local.json` — it contains local machine paths.

---

## 貢獻 Contributing

歡迎提交 Issue 或 PR，特別是：
- 新 agent 定義
- 改良的 skill 實作
- 新 Phase 擴充
- Bug fix 或邊界案例補丁

---

## 授權 License

MIT License — 自由使用、修改、再發布。
MIT License — Free to use, modify, and distribute.

---

*Built with [Claude Code](https://claude.ai/code) — Anthropic*
