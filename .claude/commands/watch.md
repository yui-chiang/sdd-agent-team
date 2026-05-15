# /watch — 即時監視所有 Agent 與 Phase 狀態
# 用法：/watch
# 執行者：E9 Operations Monitor

呼叫 `watch` skill，整合所有 agent 狀態、active sessions 和 phase gate 進度後輸出儀表板。

執行流程：
1. 呼叫 agent-radar.get_agent_roster() → 所有 agent 狀態
2. 呼叫 agent-radar.get_phase_status() → 當前 phase 和 gate 進度
3. 掃描所有 child sessions → 取得每個 session 的進度摘要
4. 整合後輸出 OPS WATCH REPORT

輸出格式：
```
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
║  [session summaries]                             ║
╠══════════════════════════════════════════════════╣
║  ⚠️ ALERTS                                       ║
║  [pending validations, stale agents, gate issues]║
╠══════════════════════════════════════════════════╣
║  NEXT ACTION (PM)                                ║
║  → [建議下一步]                                  ║
╚══════════════════════════════════════════════════╝
```

遵守規則：
- E9 只整合資訊，不做決策，不自行宣告 Gate CLEARED
- transcript 讀取失敗時標記 "unavailable"，不中斷整體報告
