# Phase Gate Checklists
# PM 在啟動每個新 Phase 前，必須確認上一個 Phase 的所有 gate items 都已 APPROVED

---

## PHASE 1 GATE — Discovery 完成條件
> 全部打勾後，才可以發出 Phase 2 的第一個 Task Spec

```
[ ] P1-MA-001  MarketReport 已交付並通過 PM Validation（OVERALL: PASS）
[ ] P1-CA-001  CompetitorReport 已交付並通過 PM Validation（OVERALL: PASS）
[ ] P1-DC-001  DomainReport 已交付並通過 PM Validation（OVERALL: PASS）
[ ] PRD v0.2 已發布（加入市場、競品 context）
[ ] DomainReport.mvp_feasibility_verdict ≠ NOT_FEASIBLE（若為 NOT_FEASIBLE，上報人工操作者）
```

**若 DomainReport 有 blockers**：
PM 必須將 `mvp_feasibility_verdict.blockers` 中的每一項加入 PRD 的 open questions，並標記 `blocking: true`，在 Phase 2 開始前須解決。

---

## PHASE 2 GATE — Product Definition 完成條件
> 全部打勾後，才可以發出 Phase 3 的第一個 Task Spec

```
[ ] P2-PS-001  MVPDefinition 已交付並通過 PM Validation（OVERALL: PASS）
[ ] must_have 功能數量 ≤ 8（超過 8 個 = PM 打回，要求 E4 縮減）
[ ] 所有 open_questions.blocking=true 的問題已解決
[ ] 每個 must_have 功能都能追溯到 MarketReport 的 pain point 或 CompetitorReport 的 whitespace
[ ] 每個 acceptance_criteria 是 binary testable（可用 yes/no 回答）
[ ] PRD v0.3 已發布（包含完整 MVP scope）
```

---

## PHASE 3 GATE — Design 完成條件
> 全部打勾後，才可以發出 Phase 4 的第一個 Task Spec
> Phase 3 為兩段串行：E5 Product Designer → E6 UI/UX Designer，兩者皆 APPROVED 才算通過

```
[ ] P3-PD-001  DesignSpec（E5）已交付並通過 PM Validation（OVERALL: PASS）
[ ] 每個 must_have 功能都有對應的 user_flow 條目
[ ] 每個 user_flow 的每個 step 都引用了 components 陣列中真實存在的 component
[ ] 色彩對比度符合 WCAG AA（≥ 4.5:1，文字對比）
[ ] error_states 至少涵蓋：404、network failure、auth failure、form validation
[ ] 所有 components 的 accessibility 欄位已填寫
[ ] P3-UX-001  UISpec（E6）已交付並通過 PM Validation（OVERALL: PASS）
[ ] UISpec.interaction_states 涵蓋所有 DesignSpec.components（無缺漏 component）
[ ] 所有 interactive elements 的 minimum_touch_target ≥ 44×44px
[ ] hover 與 focus 狀態視覺上可區分
[ ] motion_spec 為所有頁面轉場和 component mount/unmount 指定 duration 與 easing
[ ] PRD v0.4 已發布（加入設計決策，含視覺與互動規格摘要）
```

---

## PHASE 4 GATE — Engineering 完成條件
> 全部打勾後，才可以發出 Phase 5 的第一個 Task Spec
> Phase 4 為三方並行：E7（前端）、E8（後端）、E13（AI 工程，若產品含 AI 功能）
> 若產品不含 AI 功能，P4-AIE-001 可標記 NOT_APPLICABLE，無需等待

```
[ ] P4-FE-001  Frontend delivery（E7）已交付並通過 PM Validation（OVERALL: PASS）
[ ] P4-BE-001  Backend delivery（E8）已交付並通過 PM Validation（OVERALL: PASS）
[ ] P4-AIE-001 AI delivery（E13）已交付並通過 PM Validation（OVERALL: PASS）
              （若產品無 AI 功能，標記 NOT_APPLICABLE 並在 PRD known_limitations 記錄理由）
[ ] E7 FE manifest 的每個 api_contract endpoint 都已在 E8 BE manifest 中實作
[ ] E7 FE 與 E8 BE 的 api_contracts 對齊（request/response shape 一致，無 mismatch）
[ ] E13 AISpec 的 api_contracts 與 E8 BE manifest 對齊（AI 服務介面一致，無 mismatch）
[ ] 安全基線驗證通過（逐項確認）：
    [ ] 無 hardcoded secrets / credentials / tokens（含 LLM API keys）
    [ ] 所有 endpoints 有 input validation
    [ ] SQL 使用 parameterized queries 或 ORM
    [ ] auth endpoints 有 rate limiting
    [ ] CORS 配置為已知 origins
    [ ] 密碼使用 bcrypt（min cost 12）
    [ ] AI input guardrails 已實作（若有 AI 功能）
[ ] 所有 must_have 功能在前後端皆已實作（端到端可執行）
[ ] PRD v0.5 已發布（加入已知技術限制 known_limitations，含 AI 限制）
```

---

## PHASE 5 GATE — Ship 決策條件
> E8 QA Engineer 的 BugReport 通過後，PM 做最終 ship 決策

```
[ ] P5-QA-001  TestPlan 已交付並通過 PM Validation
[ ] P5-QA-002  BugReport 已交付並通過 PM Validation
[ ] ship_criteria_check.all_critical_resolved = true
    （有任何 CRITICAL bug 未解決 = 強制 DO_NOT_SHIP）
[ ] ship_criteria_check.security_tests_passed = true
[ ] ship_criteria_check.core_user_flows_complete = true
[ ] ship_criteria_check.accessibility_baseline = true
[ ] 所有 bug.blocker=true 的 bug 已 assign 並解決
[ ] ship_recommendation = SHIP 或 SHIP_WITH_FIXES（PM 最終裁決）
[ ] PRD v1.0 最終版本已發布
```

---

## Phase Gate 違規處理

```
情況                              → 處理方式
─────────────────────────────────────────────────
PM 發出 Phase N+1 spec            → 立即撤回 spec，補完 Phase N gate
  而 Phase N 未清
E7/E8 並行中一方 FAIL             → 等待 FAIL 方修改完成，兩者皆 APPROVED 才過 gate
DomainReport 有 NOT_FEASIBLE      → 上報人工操作者，系統暫停
Phase 1 三個 reports 有衝突        → PM 上報人工操作者，不自行裁決
```
