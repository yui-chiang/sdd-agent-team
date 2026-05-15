# ops-quick-commands — E9 輕量級查詢操作集合
# 合併自：assign.md, watch.md, phase.md, roster.md
# Owner: E9 Operations Monitor（查詢）+ PM（assign）

---

## /assign — 派任務給 Agent

**觸發**：`/assign [agent_id] [task_id]` 或「把 [task] 派給 [agent]」
**執行者**：PM

```
解析指令中的 agent_id 和 task_id，呼叫：
  agent-radar.assign_task(agent_id, task_id, caller_role: "PM")

成功後顯示：
  ✅ [agent_id] 已接受任務 [task_id]
  前一個任務：[previous_task 或 "無"]
  狀態已更新：idle → in_progress

失敗時顯示完整錯誤原因（agent 不存在、權限不足、非法字元等）。
```

---

## /watch — 即時監視儀表板

**觸發**：`/watch`、「現在進度？」、「大家在做什麼？」、「phase gate 過了嗎？」
**執行者**：E9 Operations Monitor

```
Step 1: agent-radar.get_agent_roster() → 所有 agent 狀態
Step 2: agent-radar.get_phase_status() → 當前 phase + gate
Step 3: session_info.list_sessions() → active child sessions
Step 4: 對每個 session 讀取進度摘要

輸出格式：
╔══════════════════════════════════════════════════╗
║  OPS WATCH REPORT — [timestamp]                  ║
╠══════════════════════════════════════════════════╣
║  Phase [N] — [name]  ▌[passed]/[total] gate items║
╠══════════════════════════════════════════════════╣
║  AGENTS                                          ║
║  🟢 idle    [agent list]                         ║
║  🔵 working [agent] → [task_id]                  ║
║  🟡 done    [agent] → [task_id] (awaiting PM)    ║
║  🔴 blocked [agent] → [reason]                   ║
╠══════════════════════════════════════════════════╣
║  ACTIVE SESSIONS                                 ║
║  [session summaries or "transcript unavailable"] ║
╠══════════════════════════════════════════════════╣
║  ⚠️ ALERTS                                       ║
║  [pending validations / stale / gate issues]     ║
╠══════════════════════════════════════════════════╣
║  NEXT ACTION (PM)                                ║
║  → [建議，E9 不執行，只建議]                     ║
╚══════════════════════════════════════════════════╝

行為規則：
- 無 active child sessions → 只顯示 roster + phase status
- transcript 讀取失敗 → 標記 "unavailable"，不中斷
- E9 不做任何決策，不自行宣告 Gate CLEARED
```

---

## /phase — 查詢 Phase Gate 進度

**觸發**：`/phase`、「phase 過了嗎？」、「現在在哪個 phase？」、「gate 還差什麼？」
**執行者**：E9 Operations Monitor

```
呼叫 agent-radar.get_phase_status()，呈現：
- 當前 phase 名稱與編號
- ✅ 已通過 / ⏳ 未通過的 gate items
- 距離 CLEARED 還差幾項
- 若全通過：「✅ Phase N GATE CLEARED，可通知 PM 啟動 Phase N+1」
- 若有未過：列出需補完的項目名稱

（更詳細的 checklist 格式請使用 /phase-gate command）
```

---

## /roster — 查詢所有 Agent 狀態

**觸發**：`/roster`、「誰是 idle？」、「現在有誰可以接任務？」
**執行者**：E9 Operations Monitor

```
呼叫 agent-radar.get_agent_roster()，以表格格式呈現：
- 依 status 分組（in_progress → blocked → done → idle）
- 顯示：agent_id, name, status, current_task, last_updated
- idle agents 標示「✅ 可接任務」
- blocked agents 顯示 notes 原因
```

---

## 行為邊界

```
E9 可執行：/watch、/phase、/roster（查詢）
PM 可執行：/assign（指派）
E9 不可執行：/assign（會變成未授權的 assign，違反 domain lock）
PM 不可自行宣告 Gate CLEARED（需先 /phase-gate 確認）
```
