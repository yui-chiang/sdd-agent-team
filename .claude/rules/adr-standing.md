# ADR Master — Pre-populated Standing Decisions
# 這 5 條 ADR 在系統初始化時即生效，不需要任何 agent 觸發
# Status: ACTIVE — 不得被 agent 自行 supersede，需人工操作者批准才能變更

---

```
ADR-S001
Date     : System initialization
Task Ref : SYSTEM
Decision : Domain Lock is absolute.
           Agent output outside its defined domain is voided and re-assigned.
Rationale: Cross-domain contamination degrades output quality and creates ownership confusion.
           Strict domain separation enables parallel execution and clear accountability.
Impact   : PM must reject any output that crosses domain boundaries, regardless of output quality.
           "I was just trying to help" is not a valid defense against a domain violation.
Status   : ACTIVE — cannot be superseded without human operator approval.
```

---

```
ADR-S002
Date     : System initialization
Task Ref : SYSTEM
Decision : PM never produces content.
           PM outputs are limited to: Task Specifications, Validation Reports, PRD updates, ADR entries.
Rationale: SDD mode requires strict separation between orchestration and execution.
           A PM that also produces content cannot objectively validate that same content.
Impact   : Any PM output containing code, design artifacts, research content, or marketing copy
           is a system error. The content must be discarded and re-assigned to the correct agent.
Status   : ACTIVE — cannot be superseded.
```

---

```
ADR-S003
Date     : System initialization
Task Ref : SYSTEM
Decision : Phase gates are strictly enforced.
           Phase N+1 cannot begin until all Phase N tasks are APPROVED.
Rationale: Downstream agents depend on complete upstream outputs.
           Partial inputs cause cascading rework that costs more time than the parallelization saves.
Impact   : PM blocks all Phase N+1 task specs until Phase N gate is cleared.
           Time pressure does not override this rule.
           If Phase 1 runs in parallel (E1/E2/E3), all three must be APPROVED before Phase 2 starts.
Status   : ACTIVE.
```

---

```
ADR-S004
Date     : System initialization
Task Ref : SYSTEM
Decision : Security violations are automatic FAIL, regardless of all other output quality.
Violations include:
  - Hardcoded secrets, credentials, API keys, or tokens in code
  - Missing input validation on any API endpoint
  - SQL queries built via string concatenation
  - Missing authentication where required
  - Missing rate limiting on authentication endpoints
  - Passwords not hashed with bcrypt (min cost 12)
  - CORS configured to allow all origins (*)
Rationale: Security cannot be patched after launch in an MVP context without full re-deployment.
           A working but insecure MVP is not shippable.
Impact   : E7 must pass security checklist before PM approves backend delivery.
           E8 must include OWASP-relevant security test cases for every product.
           E6 must not store credentials client-side or expose them in code.
Status   : ACTIVE — cannot be superseded.
```

---

```
ADR-S005
Date     : System initialization
Task Ref : SYSTEM
Decision : MVP must_have feature cap is 8 features maximum.
           Exceeding this limit requires explicit human operator approval before proceeding.
Rationale: "MVP" with more than 8 must-have features is a product, not a minimum viable product.
           Scope creep at the definition stage compounds build time across all downstream phases.
Impact   : E4 Product Strategist is bound by this cap.
           PM must reject any MVPDefinition.features.must_have array with length > 8.
           Human operator may grant a one-time exception with written justification added to ADR.
Status   : ACTIVE.
```

---

## ADR Entry Format（新 ADR 格式參考）

每當 PM 需要新增一條 ADR，使用此格式：

```
ADR-[number]
Date     : [YYYY-MM-DD]
Task Ref : [TASK_ID 或 SYSTEM]
Decision : [決策內容，一句話]
Rationale: [為什麼這樣決定，最多兩句]
Impact   : [這個決定對下游的約束或啟用]
Status   : ACTIVE | SUPERSEDED BY ADR-[n]
```

ADR 編號規則：
- `ADR-S001` 到 `ADR-S005`：系統預置，不可被 agent 覆蓋
- `ADR-P1-001` 起：Phase 1 決策
- `ADR-P2-001` 起：Phase 2 決策
- `ADR-P3-001` 起：Phase 3 決策
- `ADR-P4-001` 起：Phase 4 決策
- `ADR-P5-001` 起：Phase 5 決策
