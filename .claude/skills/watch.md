# /watch — 即時監視儀表板 Skill
# 觸發：/watch 或「現在進度？」「大家在做什麼？」
# 執行者：E9 Operations Monitor

---

## 觸發條件

- `/watch`
- 「現在進度如何？」
- 「大家在做什麼？」
- 「有誰完成了嗎？」
- 「phase gate 過了嗎？」

---

## 執行步驟

### 1. 抓 Agent 整體狀態
```
呼叫 agent-radar.get_agent_roster()
→ 取得所有 agent 的 status / current_task / last_updated
```

### 2. 抓 Phase Gate 進度
```
呼叫 agent-radar.get_phase_status()
→ 取得當前 phase 和 gate checklist
```

### 3. 掃描 Child Sessions
```
呼叫 session_info.list_sessions()
→ 找出所有 is_child: true 的 sessions

對每個 active child session：
  呼叫 session_info.read_transcript(session_id, max_wait_seconds: 0, format: "auto")
  → 取得進度摘要或最後一步
```

### 4. 整合輸出

以下格式呈現（由 E9 產出）：

```
╔══════════════════════════════════════════════════╗
║  🎯 OPS WATCH REPORT — [timestamp]               ║
╠══════════════════════════════════════════════════╣
║  Phase [N] — [name]  ▌[passed]/[total] gate items║
╠══════════════════════════════════════════════════╣
║  AGENTS                                          ║
║  🟢 idle       CEO  PM  HR  E4  E5  E6           ║
║  🔵 working    E7 → PB-BE-001                    ║
║  🔵 working    E1 → P1-MA-001                    ║
║  🟡 done       E2 → P1-CA-001 (awaiting PM)      ║
║  🔴 blocked    E3 → waiting for OQ-1             ║
╠══════════════════════════════════════════════════╣
║  ACTIVE SESSIONS                                 ║
║  E7-session: "正在實作 getAgentRoster tool..."   ║
║  E1-session: "分析 TAM/SAM/SOM，已完成 persona"  ║
╠══════════════════════════════════════════════════╣
║  ⚠️ ALERTS                                       ║
║  • E2 已完成，等待 PM Validation                  ║
╠══════════════════════════════════════════════════╣
║  NEXT ACTION (PM)                                ║
║  → Validate E2 output（P1-CA-001）              ║
╚══════════════════════════════════════════════════╝
```

---

## 行為規則

- 若無 active child sessions → 顯示全體 roster + phase status 即可
- 若 session transcript 讀取失敗 → 標記 "transcript unavailable"，不中斷整體報告
- E9 不在此 skill 內做任何決策，只整合資訊呈現
- 輸出後 E9 可加一行 "Next Action" 建議給 PM，但只是建議，不執行
