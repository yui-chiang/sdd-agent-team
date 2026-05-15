# /adr-new — 新增 ADR Entry
# 用法：/adr-new [context]
# 範例：/adr-new Phase 2 gate cleared
#       /adr-new E8 Backend APPROVED
# 執行者：PM

呼叫 `adr_manager` skill，依 $ARGUMENTS 提供的 context 產出格式正確的 ADR entry。

執行流程：
1. 解析 $ARGUMENTS 確認決策 context（phase gate、approval、scope change 等）
2. 自動指定 ADR 編號（依當前 phase：P1-001、P2-001 等，查詢現有 ADR 取得下一個 seq）
3. 填入標準 ADR 模板並輸出

若 $ARGUMENTS 為空，提示：「請說明這條 ADR 記錄的是什麼決策？」

輸出格式（adr-standing.md 定義的格式）：
```
ADR-[PHASE]-[SEQ]
Date     : [YYYY-MM-DD]
Task Ref : [TASK_ID 或 SYSTEM]
Decision : [決策內容，一句話]
Rationale: [為什麼，最多兩句]
Impact   : [對下游的約束或啟用]
Status   : ACTIVE
```

觸發時機提醒：
- Phase Gate 清除後
- 任何 agent 輸出被 APPROVED 後
- Scope 變更（功能新增、移除、延後）
- 安全或合規決策
- Ship 決策

遵守規則：
- PRINCIPLE-04：未寫入 ADR = 視為未做決定
- 每條 ADR 只記錄一個決策，不合併多個決策
