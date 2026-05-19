# /phase — 查詢 Phase Gate 進度
# 觸發：/phase 或「phase 過了嗎？」「現在在哪個 phase？」「gate 還差什麼？」

呼叫 agent-radar.get_phase_status()，呈現：
- 當前 phase 名稱與編號
- 已通過 / 未通過的 gate items（以 ✅ / ⏳ 標示）
- 距離 CLEARED 還差幾項
- 若全部通過：顯示「✅ Phase N GATE CLEARED，可通知 PM 啟動 Phase N+1」
- 若有 FAIL items：列出需要補完的項目名稱
