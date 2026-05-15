# /assign — PM 將任務指派給 Agent
# 用法：/assign [agent_id] [task_id]
# 範例：/assign E7 P4-FE-001
#       /assign E10 P5-QA-001
# 執行者：PM

呼叫 `assign` skill，解析 $ARGUMENTS 並執行任務指派。

執行流程：
1. 解析 $ARGUMENTS 取得 agent_id 和 task_id
2. 確認 agent 狀態為 idle（若 in_progress 則警告）
3. 確認 Phase Gate 允許此任務（Phase N+1 任務不得在 Phase N gate 未清前派工）
4. 呼叫 agent-radar.assign_task(agent_id, task_id, caller_role: "PM")
5. 輸出確認

輸出格式：
```
✅ [agent_id] 已接受任務 [task_id]
   Agent: [agent_name]
   Task: [task_id]
   前一個任務: [previous_task 或 "無"]
   狀態: idle → in_progress
   
⚠️ 下一步: 開啟 child session（/pm-orchestrator）並通知 E9 監視
```

錯誤情況：
```
❌ ASSIGN FAILED
   原因: [agent 不存在 / 狀態非 idle / Phase Gate 未清 / task_id 格式錯誤]
   建議: [修正方法]
```

遵守規則：
- RULE-PM-06：Phase N 未全 APPROVED 不得 assign Phase N+1 任務
- 只有 PM（caller_role: PM）可以 assign，E9 可通知但不可 assign
