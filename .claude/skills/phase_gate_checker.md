# Skill: phase_gate_checker
# Owner: PM | 用途：在所有當前 Phase 任務 APPROVED 前，阻止 Phase 轉換

---

## 目的

確保 Phase Gate 規則（ADR-S003）被嚴格執行，防止在上游輸出未完成前啟動下游工作，避免連鎖返工。

---

## Phase Gate 執行流程

```
當 PM 準備發出 Phase N+1 的第一個 Task Spec 時：

Step 1: 列出 Phase N 的所有任務
Step 2: 確認每個任務的狀態
Step 3: 若所有任務 = APPROVED → Gate 清除，可以 spec Phase N+1
Step 4: 若任何任務 ≠ APPROVED → Gate 未清，拒絕發出 Phase N+1 spec
Step 5: Gate 清除後，更新 ADR 和 PRD 的 Phase Gate Status
```

---

## Gate Checklist

### Phase 1 Gate

```
PHASE 1 GATE CHECK
══════════════════════════════════════════════════════
[ ] P1-MA-001  MarketReport        Status: OPEN / APPROVED
[ ] P1-CA-001  CompetitorReport    Status: OPEN / APPROVED
[ ] P1-DC-001  DomainReport        Status: OPEN / APPROVED
[ ] ADR 已更新（Phase 1 決策已記錄）
[ ] PRD v0.2 已發布

Gates Cleared: [0/5]
Result: ❌ GATE NOT CLEARED — Phase 2 cannot begin
        ✅ GATE CLEARED — Phase 2 may begin
══════════════════════════════════════════════════════
Special Check: DomainReport.mvp_feasibility_verdict.verdict
  FEASIBLE → Continue
  FEASIBLE_WITH_CONSTRAINTS → Continue, resolve blockers in Phase 2
  HIGH_RISK → Escalate to human operator before Phase 2
  NOT_FEASIBLE → STOP, escalate to human operator, system pause
```

---

### Phase 2 Gate

```
PHASE 2 GATE CHECK
══════════════════════════════════════════════════════
[ ] P2-PS-001  MVPDefinition       Status: OPEN / APPROVED
[ ] must_have 功能數量 ≤ 8
[ ] 所有 open_questions.blocking=true 已解決
[ ] ADR 已更新
[ ] PRD v0.3 已發布

Gates Cleared: [0/5]
Result: ❌ GATE NOT CLEARED / ✅ GATE CLEARED
══════════════════════════════════════════════════════
```

---

### Phase 3 Gate

```
PHASE 3 GATE CHECK
══════════════════════════════════════════════════════
[ ] P3-PD-001  DesignSpec          Status: OPEN / APPROVED
[ ] 每個 must_have 功能有對應 user_flow
[ ] 所有 user_flow steps 引用真實 components
[ ] error_states 最低要求：404, network, auth, validation
[ ] ADR 已更新
[ ] PRD v0.4 已發布

Gates Cleared: [0/6]
Result: ❌ GATE NOT CLEARED / ✅ GATE CLEARED
══════════════════════════════════════════════════════
```

---

### Phase 4 Gate

```
PHASE 4 GATE CHECK
══════════════════════════════════════════════════════
[ ] P4-FE-001  Frontend delivery   Status: OPEN / APPROVED
[ ] P4-BE-001  Backend delivery    Status: OPEN / APPROVED
[ ] FE api_contracts 與 BE api_endpoints 完全對齊
[ ] 安全基線：
    [ ] 無 hardcoded secrets
    [ ] Input validation 存在
    [ ] Parameterized queries
    [ ] Rate limiting on auth
    [ ] CORS configured
    [ ] bcrypt passwords
[ ] ADR 已更新
[ ] PRD v0.5 已發布

Gates Cleared: [0/8]
Result: ❌ GATE NOT CLEARED / ✅ GATE CLEARED
══════════════════════════════════════════════════════
Note: E6 和 E7 必須「雙雙 APPROVED」，不接受「先讓先過的繼續」
```

---

### Phase 5 Gate（Ship 決策）

```
PHASE 5 GATE CHECK（Ship Criteria）
══════════════════════════════════════════════════════
[ ] P5-QA-001  TestPlan            Status: OPEN / APPROVED
[ ] P5-QA-002  BugReport           Status: OPEN / APPROVED
[ ] ship_criteria_check.all_critical_resolved = true
[ ] ship_criteria_check.security_tests_passed = true
[ ] ship_criteria_check.core_user_flows_complete = true
[ ] ship_criteria_check.accessibility_baseline = true
[ ] 所有 blocker=true bugs 已解決
[ ] ship_recommendation ≠ DO_NOT_SHIP
[ ] ADR 已更新（ship 決策記錄）
[ ] PRD v1.0 已發布

Gates Cleared: [0/10]
Ship Decision: ❌ DO NOT SHIP / ✅ SHIP / ⚠️ SHIP WITH FIXES
══════════════════════════════════════════════════════
Hard Rule: 任何 CRITICAL bug 未解決 = 強制 DO_NOT_SHIP，無視其他 gate items
```

---

## Gate Violation 處理

```
情況                                     → 處理
───────────────────────────────────────────────────────────────
PM 發出 Phase N+1 spec 而 N 未清         → 立即撤回 spec，補完 gate
E2 未等 E1 就完成（僅依 product idea）  → 可接受（E2 有 product idea 可用），
                                            但 MarketReport 是更好的輸入，
                                            E2 應標記此限制在 known_limitations
E6/E7 之一 FAIL，另一 APPROVED          → 等待 FAIL 方修改，兩者皆 APPROVED 才過 gate
DomainReport verdict = HIGH_RISK         → 上報人工操作者，等待人工決策後才繼續
任何 Phase 5 CRITICAL bug 存在          → PM 不得 ship，必須打回 E6 或 E7 修復
```

---

## Gate 清除後的動作清單

當 Phase Gate 清除，PM 必須依序執行：

```
1. 在 ADR 新增 "Phase N Gate Cleared" 條目
2. 更新 PRD 的 Phase Gate Status 表格
3. 通知相關 stakeholders（若有）
4. 立即 spec Phase N+1 的第一個任務
5. （若 Phase N+1 有並行任務）同時 spec 所有並行任務
```
