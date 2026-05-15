# /spec — PM 產出 Task Specification
# 用法：/spec [phase] [agent_code]
# 範例：/spec P4 FE  → 產出 P4-FE-001 的 Task Spec
#       /spec P1 MA  → 產出 P1-MA-001 的 Task Spec
# 執行者：PM

呼叫 `spec_writer` skill，依 $ARGUMENTS 指定的 Phase 和 Agent Code 產出完整 Task Specification。

執行流程：
1. 解析 $ARGUMENTS 取得 phase（P1-P6）和 agent_code（MA/CA/DC/PS/PD/FE/BE/QA/AIE 等）
2. 自動產生 task_id（格式：[PHASE_CODE]-[AGENT_CODE]-[SEQ]）
3. 帶入對應 phase 的標準 AC 參考（來自 spec_writer.md）
4. 輸出 Task Spec（=== 格式）

若未提供參數，列出當前 phase 可以執行的任務，由操作者選擇。

輸出格式（=== 格式）：
```
===
TASK_ID     : [PHASE]-[CODE]-[SEQ]
ASSIGNED_TO : [agent_id]
PHASE       : [phase name]
OBJECTIVE   : [一句話目標]
INPUTS      : [上游輸出清單]
DELIVERABLE : [輸出格式]
ACCEPTANCE_CRITERIA:
  AC1: [binary testable 條件]
  AC2: ...
DEADLINE    : [若已知]
===
```

遵守規則：
- RULE-PM-01：PM 不寫 code，只寫 spec
- PRINCIPLE-03：Phase N+1 的 spec 不得在 Phase N gate 未清前發出
- 每條 AC 必須是 binary testable（yes/no 可回答）
- spec 完成後提醒：「記得同步更新 PRD 版本（prd_maintainer.md）」
