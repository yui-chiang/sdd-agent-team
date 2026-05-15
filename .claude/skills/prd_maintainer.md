# Skill: prd_maintainer
# Owner: PM | 用途：跨 phase 維護 PRD 為 living document

---

## 目的

確保 PRD 在每個 phase 完成後都即時更新，作為所有 stakeholder 了解目前 MVP 狀態的唯一文件。

---

## PRD 版本觸發點

| PRD 版本 | 觸發條件 | 主要更新內容 |
|---|---|---|
| **v0.1** | PM 收到 product idea 後立即建立 | Product idea 描述、初步假設、已知約束 |
| **v0.2** | Phase 1 Gate 清除後 | 加入 Market context、競品分析、domain 約束、可行性裁定 |
| **v0.3** | Phase 2 Gate 清除後 | 加入完整 MVP scope（must/should/out_of_scope）、success metrics |
| **v0.4** | Phase 3 Gate 清除後 | 加入設計決策、IA 結構、component 決策 |
| **v0.5** | Phase 4 Gate 清除後 | 加入已知技術限制、API contracts、env variables 清單 |
| **v1.0** | Phase 5 Gate 清除後 | 最終版本，加入 QA 結果、ship 決策、已知 bugs |

---

## PRD 完整模板

```markdown
# Product Requirements Document (PRD)
# Maintained by: PM
# System: MVP Build Multi-Agent System v1.0

## Version History
| Version | Phase | Date | Summary |
|---|---|---|---|
| 0.1 | 0 | [date] | Initial draft |

---

## 1. Product Overview
### Working Name
[from E4 MVPDefinition.product_name]

### One-Liner
[from E4 MVPDefinition.one_liner]

### Core Problem
[from E4 MVPDefinition.core_problem]

### MVP Goal
[from E4 MVPDefinition.mvp_goal]

---

## 2. Market Context
### Market Size
- TAM: [from E1 MarketReport.tam_estimate]
- SAM: [from E1 MarketReport.sam_estimate]
- SOM: [from E1 MarketReport.som_estimate]

### Primary Persona
- Name: [from E1 MarketReport.primary_persona.name]
- Role: [from E1]
- Top Pain Points: [from E1]
- Current Solution: [from E1]
- Trigger: [from E1]

### Key Market Trends
[from E1 MarketReport.market_trends — list tailwinds and headwinds]

---

## 3. Competitive Position
### Key Competitors
[from E2 CompetitorReport.competitors — names and types]

### Whitespace We Target
[from E2 CompetitorReport.whitespace — the gaps we are filling]

### Our Differentiator
[from E4 MVPDefinition.value_proposition.differentiator]

---

## 4. Domain Constraints
### Regulatory Requirements
[from E3 DomainReport.regulatory_requirements — applies_to_mvp=true only]

### Technical Constraints
[from E3 DomainReport.technical_constraints]

### Feasibility Verdict
[from E3 DomainReport.mvp_feasibility_verdict]

---

## 5. MVP Feature Scope

### Must Have (≤8)
| ID | Feature | User Story | Key AC |
|---|---|---|---|
[from E4 MVPDefinition.features.must_have]

### Should Have
[from E4 MVPDefinition.features.should_have]

### Out of Scope
| Feature | Reason |
|---|---|
[from E4 MVPDefinition.features.out_of_scope]

---

## 6. Success Metrics
| Metric | Target | Timeframe |
|---|---|---|
[from E4 MVPDefinition.success_metrics]

---

## 7. Open Questions
| Question | Owner | Blocking | Status |
|---|---|---|---|
[from E4 MVPDefinition.open_questions — updated each phase]

---

## 8. Technical Summary (added in v0.5)
### Architecture
[from E7 Backend manifest.architecture]

### Known Limitations
[from E6/E7 manifest.known_limitations]

### Required Environment Variables
[merged from E6 and E7 env_variables]

---

## 9. QA Summary (added in v1.0)
### Ship Recommendation
[from E8 BugReport.summary.ship_recommendation]

### Ship Criteria Check
[from E8 BugReport.ship_criteria_check]

### Outstanding Bugs
[from E8 BugReport.bugs where blocker=true]

---

## 10. Phase Gate Status
| Phase | Status | Date Closed | Gate Items |
|---|---|---|---|
| 1 - Discovery | OPEN | — | MarketReport ✗ / CompetitorReport ✗ / DomainReport ✗ |
| 2 - Definition | OPEN | — | MVPDefinition ✗ |
| 3 - Design | OPEN | — | DesignSpec ✗ |
| 4 - Engineering | OPEN | — | Frontend ✗ / Backend ✗ |
| 5 - QA | OPEN | — | TestPlan ✗ / BugReport ✗ |
```

---

## PRD 更新規則

1. **只追加，不刪除**：PRD 是 living document，歷史版本保留
2. **只填入已驗證的資料**：只有 APPROVED 的 agent 輸出才能更新 PRD
3. **PM 不改寫 agent 輸出**：PRD 中的內容從 agent 輸出轉錄，不加工
4. **Open questions 持續更新**：每個 phase 後確認哪些問題已解決，哪些新問題出現
5. **Phase Gate Status 即時更新**：每當一個 gate item 被清除，立即更新狀態
