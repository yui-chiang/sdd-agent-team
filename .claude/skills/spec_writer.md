# Skill: spec_writer
# Owner: PM | 用途：產出含可測試 Acceptance Criteria 的 Task Specification

---

## 目的

為每個任務產出結構化的 Task Specification，確保每個 AC 是可測試的 binary 問題（yes/no），並包含足夠的 context 讓 agent 不需要額外溝通即可執行。

---

## Task Specification 模板

```
===
TASK_ID    : [PHASE_CODE]-[AGENT_CODE]-[SEQ]
ASSIGNED_TO: [Agent name and code]
PHASE      : [1-5]
DEPENDS_ON : [TASK_ID or NONE]
PRIORITY   : CRITICAL | HIGH | MEDIUM
INPUT      : [完整說明 agent 將收到什麼文件/資料]
OBJECTIVE  : [一句話描述成功的樣子]
DELIVERABLE: [精確的輸出格式和名稱]
ACCEPTANCE_CRITERIA:
  AC1: [具體、binary、可測試 — 可用 yes/no 回答]
  AC2: ...
  ACn: ...
CONSTRAINTS:
  - [針對此 agent 的 domain lock 提醒]
  - [適用此任務的 PRD 約束或 phase-gate 條件]
NOTES      : [對 agent 有幫助的背景 context]
===
```

---

## 如何寫好 AC（Acceptance Criteria）

### AC 的黃金標準
每條 AC 必須能用 yes/no 回答，不能有「差不多」的答案。

**好的 AC 範例：**
```
AC1: MarketReport JSON 通過 JSON.parse() 驗證，無語法錯誤
AC2: tam_estimate.size 欄位包含數值和貨幣單位（e.g. '$4.2B'），非 INSUFFICIENT_DATA
AC3: primary_persona.top_pain_points 陣列包含 3-5 個條目
AC4: regulatory_flags 欄位存在且為陣列（可為空陣列）
AC5: 輸出不含任何 Markdown fence（```）或 preamble 文字
```

**壞的 AC 範例：**
```
✗ AC1: 市場分析要夠詳細（不可測試）
✗ AC2: persona 要真實（主觀）
✗ AC3: JSON 格式要正確（太模糊）
```

---

## 每個 Phase 的標準 AC 參考

### Phase 1 — E1 MarketReport AC 參考
```
AC1: 輸出是合法 JSON，通過 JSON.parse() 驗證
AC2: 以下所有欄位存在且非空：product_category, problem_statement, tam_estimate, sam_estimate, som_estimate, primary_persona
AC3: tam_estimate 包含 size, source, confidence 三個子欄位
AC4: primary_persona.top_pain_points 包含 3-5 個具體 pain points
AC5: regulatory_flags 欄位存在（可為空陣列 []）
AC6: data_gaps 欄位存在，所有 INSUFFICIENT_DATA 的欄位已加入此陣列
```

### Phase 1 — E2 CompetitorReport AC 參考
```
AC1: 輸出是合法 JSON
AC2: competitors 陣列包含至少 2 個真實競品
AC3: 每個 whitespace 條目都有非空的 evidence 欄位
AC4: feature_matrix 包含 [THIS PRODUCT] 作為最後一行
AC5: differentiation_angles 陣列基於 whitespace gaps，非泛用建議
```

### Phase 1 — E3 DomainReport AC 參考
```
AC1: 輸出是合法 JSON
AC2: mvp_feasibility_verdict 欄位存在且 verdict 為有效值
AC3: 若有 regulatory_requirements，每條都包含 applies_to_mvp 欄位
AC4: 所有 regulatory_requirements 引用真實法規（無捏造）
AC5: 若 blockers 非空，每個 blocker 都有具體的解決方向
```

### Phase 2 — E4 MVPDefinition AC 參考
```
AC1: 輸出是合法 JSON
AC2: must_have 功能數量 ≤ 8
AC3: 每個 must_have 功能有 whitespace_ref 或可追溯的 pain point
AC4: 每個 acceptance_criteria 是 binary testable
AC5: out_of_scope 的每個條目都有明確的 reason
AC6: success_metrics 每條都有 metric、target、timeframe
```

### Phase 3 — E5 DesignSpec AC 參考
```
AC1: 輸出是合法 JSON
AC2: 每個 MVPDefinition.must_have 功能在 user_flows 中有對應條目
AC3: 每個 user_flow step 引用的 component 存在於 components 陣列中
AC4: error_states 包含 404、network_error、auth_failure、validation_error（至少這四種）
AC5: 所有 components 的 accessibility 欄位已填寫
```

### Phase 4 — E6 Frontend AC 參考
```
AC1: Delivery manifest 是合法 JSON
AC2: 所有 MVPDefinition.must_have 功能在 manifest.files 中有對應 feature_refs
AC3: Code 中無 console.log()（grep 驗證）
AC4: Code 中無 hardcoded credentials / secrets（grep 驗證）
AC5: api_contracts 的每個 endpoint 格式完整（endpoint, called_from, request, response）
```

### Phase 4 — E7 Backend AC 參考
```
AC1: Delivery manifest 是合法 JSON
AC2: api_endpoints 覆蓋 E6 manifest 中的所有 api_contracts
AC3: database_schema 支援所有 must_have 功能的資料需求
AC4: 安全基線全部到位（input validation、parameterized queries、rate limiting、bcrypt、CORS）
AC5: Code 中無 hardcoded secrets
```

### Phase 5 — E8 TestPlan AC 參考
```
AC1: TestPlan 是合法 JSON
AC2: 每個 must_have 功能有至少 3 個 test cases（FUNCTIONAL + EDGE_CASE + NEGATIVE）
AC3: security_test_cases 涵蓋 SQL injection、XSS、auth bypass、IDOR
AC4: 每個 test_case 的 maps_to_ac 可追溯到 MVPDefinition 的 AC
```

---

## Spec 品質檢查清單

提交前確認：
```
[ ] TASK_ID 符合命名規範
[ ] INPUT 欄位明確說明 agent 收到什麼（不是「相關文件」這種模糊描述）
[ ] 每個 AC 可用 yes/no 回答
[ ] CONSTRAINTS 包含相關的 PRD 約束或 phase-gate 條件
[ ] NOTES 提供 agent 需要的背景 context（但不洩漏答案）
```
