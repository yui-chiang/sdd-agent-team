# /assign — PM 派任務給 Agent
# 觸發：/assign [agent_id] [task_id] 或「把 [task] 派給 [agent]」
# 執行者：PM（caller_role: PM）

解析指令中的 agent_id 和 task_id，呼叫：
  agent-radar.assign_task(agent_id, task_id, caller_role: "PM")

成功後顯示：
  ✅ [agent_id] 已接受任務 [task_id]
  前一個任務：[previous_task 或 "無"]
  狀態已更新：idle → in_progress

失敗時顯示完整錯誤原因（agent 不存在、權限不足、非法字元等）。

使用範例：
  /assign E7 PB-BE-001
  /assign PM PA-HR-001
