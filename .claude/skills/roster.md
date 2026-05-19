# /roster — 查詢所有 Agent 狀態
# 觸發：/roster 或「誰是 idle？」「現在有誰可以接任務？」

呼叫 agent-radar.get_agent_roster()，以表格格式呈現：
- 依 status 分組（in_progress → blocked → done → idle）
- 顯示：agent_id, name, status, current_task, last_updated
- idle 的 agent 特別標示「✅ 可接任務」
- blocked 的 agent 顯示 notes 原因
