# /roster — 查詢所有 Agent 狀態
# 用法：/roster

呼叫 `roster` skill，以表格格式呈現所有 agent 的當前狀態。

執行流程：
1. 呼叫 agent-radar.get_agent_roster()
2. 依 status 分組排序（in_progress → blocked → done → idle）
3. 輸出表格

輸出格式：
```
AGENT ROSTER — [timestamp]
══════════════════════════════════════════════════════
STATUS      AGENT   NAME                 CURRENT TASK
🔵 working  E7      Frontend Engineer    P4-FE-001
🟡 done     E2      Competitor Analyst   P1-CA-001 ← awaiting PM
🔴 blocked  E3      Domain Consultant    waiting for P1-CA-001
🟢 idle     CEO     CEO                  —
🟢 idle     PM      PM                   —
🟢 idle     E1      Market Analyst       —
...
══════════════════════════════════════════════════════
可接任務的 agents（idle）: CEO, PM, E1, E4, E5, ...
```

遵守規則：
- blocked agents 必須顯示 notes 原因
