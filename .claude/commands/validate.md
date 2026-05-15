# /validate — PM 驗收 Agent 輸出
# 用法：/validate [task_id]
# 執行者：PM

呼叫 `validation_engine` skill，對 $ARGUMENTS 指定的 task_id 執行逐條 AC 驗證。

執行流程：
1. 找到 task_id 對應的 Task Specification（確認 AC 清單）
2. 找到對應 agent 的最新輸出
3. 逐條核查每條 AC（binary PASS / FAIL）
4. 輸出 Validation Report（=== 格式）

若未提供 task_id，列出目前所有 status=done 且等待 validation 的任務，由操作者選擇。

輸出格式（validation_engine.md 規定的 === 格式）：
```
===
TASK_ID    : [task_id]
VALIDATOR  : PM
TIMESTAMP  : [YYYY-MM-DD HH:MM]
OVERALL    : PASS | FAIL
RESULTS:
  AC1: PASS | FAIL — [說明]
  ...
DECISION   : APPROVED | REVISION_REQUIRED
===
```

遵守規則：
- PRINCIPLE-05：驗證是二元的，無「部分通過」
- RULE-PM-05：有任何 AC 未測試就 APPROVE = 違規
