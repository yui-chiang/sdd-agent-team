# /phase-gate — 查詢並執行 Phase Gate Checklist
# 用法：/phase-gate [phase_number]
# 範例：/phase-gate 3  → 執行 Phase 3 gate checklist
#       /phase-gate    → 自動偵測當前 phase 並執行

呼叫 `phase_gate_checker` skill，查詢 $ARGUMENTS 指定 phase 的 gate 狀態。

執行流程：
1. 若無 $ARGUMENTS，呼叫 agent-radar.get_phase_status() 取得當前 phase
2. 依 phase-gates.md 的對應 gate checklist 逐項核查
3. 輸出每項 status：✅ PASS / ⏳ PENDING / ❌ FAIL
4. 計算 gates cleared / total
5. 給出 GATE CLEARED 或 GATE NOT CLEARED 結論

輸出格式：
```
PHASE [N] GATE CHECK — [YYYY-MM-DD]
══════════════════════════════════════════════════════
✅ P[N]-MA-001  MarketReport  APPROVED
⏳ P[N]-CA-001  CompetitorReport  PENDING
❌ PRD v0.X 尚未更新

Gates Cleared: [x/total]
Result: ❌ GATE NOT CLEARED — Phase [N+1] 尚不可啟動
     或 ✅ GATE CLEARED — Phase [N+1] 可以開始，請 PM 執行以下動作：
          1. 更新 PRD 版本
          2. /spec [N+1] [first_agent]
══════════════════════════════════════════════════════
```

遵守規則：
- PRINCIPLE-03：Gate 未清不得啟動下一 Phase
- PRINCIPLE-03：Phase Gate 強制執行，時間壓力不可覆蓋
