# Skill: task_decomposer
# Owner: PM | 用途：將 product idea 拆解成有依賴關係的原子任務

---

## 目的

將一個 product idea 拆解成 5 個 phases 的結構化執行計劃，每個任務有明確的依賴關係、優先順序和責任 agent。

---

## 輸入

- Product idea（文字描述）
- 任何已知的約束（技術、法規、時間）

---

## 輸出格式

```
PHASED_EXECUTION_PLAN
Product: [product name or working title]
Created: [date]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━

PHASE 1 — DISCOVERY（依序啟動，非嚴格並行）
  注意：E1 無依賴可立即啟動；E2 需要 E1 輸出；E3 需要 E1+E2 輸出。
        三者儘早啟動以縮短時程，但依賴關係必須滿足。

  Task: P1-MA-001
    Assigned: E1 Market Research Analyst
    Depends: NONE
    Priority: CRITICAL
    Estimated complexity: [HIGH | MEDIUM | LOW]

  Task: P1-CA-001
    Assigned: E2 Competitor Analyst
    Depends: P1-MA-001（需要 MarketReport 作為輸入）
    Priority: CRITICAL
    Estimated complexity: [HIGH | MEDIUM | LOW]

  Task: P1-DC-001
    Assigned: E3 Domain Consultant
    Depends: P1-MA-001, P1-CA-001（需要兩份報告）
    Priority: CRITICAL
    Estimated complexity: [HIGH | MEDIUM | LOW]

PHASE 2 — PRODUCT DEFINITION
  Task: P2-PS-001
    Assigned: E4 Product Strategist
    Depends: P1-MA-001, P1-CA-001, P1-DC-001（Phase 1 Gate 清除）
    Priority: CRITICAL
    Estimated complexity: HIGH

PHASE 3 — DESIGN（串行：E5 → E6）
  Task: P3-PD-001
    Assigned: E5 Product Designer
    Depends: P2-PS-001（Phase 2 Gate 清除）
    Priority: CRITICAL
    Estimated complexity: HIGH

  Task: P3-UX-001
    Assigned: E6 UI/UX Designer
    Depends: P3-PD-001（E5 APPROVED）
    Priority: CRITICAL
    Estimated complexity: HIGH

PHASE 4 — ENGINEERING（並行）
  Task: P4-FE-001
    Assigned: E7 Frontend Engineer
    Depends: P3-UX-001（Phase 3 Gate 清除）
    Priority: CRITICAL
    Estimated complexity: HIGH

  Task: P4-BE-001
    Assigned: E8 Backend Engineer
    Depends: P3-UX-001（Phase 3 Gate 清除）
    Priority: CRITICAL
    Estimated complexity: HIGH

PHASE 5 — QUALITY ASSURANCE
  Task: P5-QA-001 (TestPlan)
    Assigned: E10 QA Engineer
    Depends: P4-FE-001, P4-BE-001（Phase 4 Gate 清除）
    Priority: CRITICAL
    Estimated complexity: MEDIUM

  Task: P5-QA-002 (BugReport)
    Assigned: E10 QA Engineer
    Depends: P5-QA-001（TestPlan APPROVED）
    Priority: CRITICAL
    Estimated complexity: MEDIUM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Total tasks: 10 | Critical path length: 5 phases
```

---

## 執行規則

1. **依賴關係是絕對的**：DEPENDS 中列出的任務必須 APPROVED 才能開始
2. **並行任務識別**：Phase 4（E7/E8）可並行；Phase 1 依序啟動（E1 無依賴，E2 需 E1，E3 需 E1+E2）
3. **不拆分不存在的任務**：計劃只包含此系統定義的 9 個標準任務，不額外發明任務
4. **複雜度估算**：基於 domain 複雜度和 product idea 的清晰度，標記 HIGH/MEDIUM/LOW
5. **Phase 1 依賴鏈**：E1 無依賴立即啟動；E2 需要 E1 完成 MarketReport；E3 需要 E1 和 E2 兩份報告。盡量縮短等待，但依賴不可跳過

---

## 常見錯誤

- ✗ 把 Phase 2 的任務分給 Phase 1 的 agents（E1/E2/E3）
- ✗ 在 Phase 1 gate 清除前 spec Phase 2 任務
- ✗ 為單一 agent 創建多個 tasks（標準系統每個 phase 每個 agent 只有一個 task）
- ✗ 省略依賴關係讓任務看起來可更早開始
