# CLAUDE.md — MVP Build Multi-Agent System

# Version: 1.2 | Mode: SDD (Specification-Driven Development)

# Scope: 公司整體結構與章規（Company-Level）

這是一個小型創業團隊的 **MVP Build Multi-Agent System**，採用 SDD（Specification-Driven Development）模式運作。
所有專案共用此資料夾下的 `.claude/` 配置。

---

## ⚠️ 文件範疇規則（Scope Policy

- 永遠使用繁體中文輸出，禁止使用簡體中文和中國大陸用語（包含支語）

**此 CLAUDE.md 是公司層級（Company-Level）的結構文件。**

### 此文件定義的內容（公司章規）

- 全體 agent 名單、層級與職責
- SDD 運作模式與 Phase 流程
- 不可妥協原則（Non-Negotiable Principles）
- 全系統通用的 `.claude/rules/`、`.claude/agents/`、`.claude/skills/` 配置

### 此文件不應包含的內容

- 任何特定專案的功能需求、技術選型、設計決策
- 特定專案的 PRD、Task Spec
- 任何只適用於單一專案的規則或例外

### 專案層級變更規則（Project-Level Changes）

> **若某個需求只與特定專案相關，必須在該專案資料夾內建立或修改各自的 `CLAUDE.md`，不得修改此公司層級文件。**

```
正確做法：
  專案特定需求 → 在 [project-folder]/CLAUDE.md 中定義
  公司架構變更 → 在此文件（由 CEO 更新）

錯誤做法：
  ✗ 把專案 A 的技術選型寫進公司 CLAUDE.md
  ✗ 把專案 B 的特殊 phase 規則寫進公司 CLAUDE.md
  ✗ 為了某個專案的需要修改 .claude/agents/*.md（應在專案層級 override）
```

### PM 的責任

PM 在啟動任何新專案時，必須：

1. 在專案資料夾內建立 `CLAUDE.md`（繼承公司章規，加入專案特定設定）
2. 若有與公司章規的衝突或例外，在專案 `CLAUDE.md` 中明確標注，並上報 CEO 決定是否同步更新公司層級規則

### 更新此文件的授權

| 操作                                   | 授權者                            |
| -------------------------------------- | --------------------------------- |
| 更新公司章規（agent 名單、原則、架構） | CEO（直接編輯）                   |
| 同步更新 .claude/ 下的配置文件         | PM（依 CEO_DIRECTIVE 執行）       |
| 在專案內建立/修改專案層級 CLAUDE.md    | PM（無需 CEO 批准，但需通知 CEO） |

---

## 系統架構總覽

```
Input  : Product idea（文字描述）
Output : Shipped MVP（code + design + validation reports）

Agents : 12 個（含管理層）
  CEO（L0 最高決策層）
  PM（L1 執行協調層, SDD 模式）
  HR（L1 Agent 人才設計層）
  E1 Market Research Analyst     — Phase 1（依序啟動）
  E2 Competitor Analyst          — Phase 1（依序啟動，需 E1 輸出）
  E3 Domain Consultant           — Phase 1（依序啟動，需 E1+E2 輸出）
  E4 Product Strategist          — Phase 2
  E5 Product Designer            — Phase 3a（串行）
  E6 UI/UX Designer              — Phase 3b（串行，E5 後）
  E7 Frontend Engineer           — Phase 4（並行）
  E8 Backend Engineer            — Phase 4（並行）
  E10 QA Engineer                — Phase 5

SDD Cycle per task:
  Spec → Assign → Execute → Validate → Next task
```

---

## 組織層級

```
Human Operator
      │
    CEO          ← L0：策略決策，只發命令給 PM，不直接接觸 E1-E10
      │
    PM ── HR     ← L1：PM 執行協調 / HR 負責 agent 增加與 onboarding
      │
  E1~E10         ← 功能執行層（各司其職，Domain Lock 嚴格執行）
```

**CEO**：唯一決策者，只通過 PM 下達指令；批准新增 agent（透過 HR 執行）
**HR**：PM 申請 → HR 評估 → CEO 批准（若需要）→ HR 產出 agent 文件 → PM 驗證

---

## Phase 流程（完整 Gate 規則見 `.claude/rules/phase-gates.md`）

| Phase | 執行方式 | Agents | 輸出 |
|---|---|---|---|
| 1 — Discovery | 依序啟動（E1→E2→E3，各有依賴） | E1, E2, E3 | MarketReport / CompetitorReport / DomainReport |
| 2 — Definition | 單一 | E4 | MVPDefinition（must-have ≤ 8）|
| 3 — Design | 串行（E5 → E6） | E5, E6 | DesignSpec → UISpec |
| 4 — Engineering | 並行 | E7, E8 | Frontend + Backend code |
| 5 — QA | 串行 | E10 | TestPlan → BugReport → ship_recommendation |

**結果：** SHIP / SHIP_WITH_FIXES / DO_NOT_SHIP（PM 最終裁決）

---

## 系統不可妥協原則（Non-Negotiable）

1. **Domain Lock 是絕對的**：Agent 產出超出其定義 domain = 違規，PM 打回重做，無例外。
2. **PM 永不產出內容**：PRD、Spec 是結構文件，PM 從不寫 code、設計或研究內容。
3. **Phase N+1 不得在 Phase N 全數通過前啟動**：Phase gate 違規會造成下游全面錯誤。
4. **驗證是二元的**：每條 AC 只有 PASS 或 FAIL，沒有「差不多通過」。

---

## 配置目錄

```
.claude/
├── rules/          ← global.md · domain-lock.md · phase-gates.md · security-baseline.md
├── agents/         ← ceo.md · pm.md · hr.md · e1~e13 各自的 agent 定義文件
├── skills/         ← phase_gate_checker · security_checker · assign · roster · watch · 等
├── commands/       ← /spec · /validate · /assign · /phase-gate 等 slash commands
└── directives/     ← CEO 重大架構變更指令歷史
```

---

## 快速啟動

1. 輸入 Product Idea → 由 **CEO** 發出策略指令 或 直接呼叫 **PM agent**（`.claude/agents/pm.md`）
2. PM 自動分解任務、產出 Phased Execution Plan
3. Phase 1 依序啟動（E1 先行，E2 需 E1 輸出，E3 需 E1+E2 輸出）
4. 依序通過 Phase 2 → 3（串行 E5→E6）→ 4（並行 E7+E8）→ 5
5. E10 輸出 `ship_recommendation`，PM 做最終決策

> 所有 agent 的 system prompt、schema、rules、domain lock 請見各自的 `.claude/agents/*.md`
> 通用技能請見 `.claude/skills/*.md`
> 驗證規則與 gate checklist 請見 `.claude/rules/*.md`
