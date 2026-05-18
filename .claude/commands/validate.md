# /validate — PM 驗收 Agent 輸出
# 用法：/validate [task_id]
# 執行者：PM

PM 對 $ARGUMENTS 指定的 task_id 執行逐條 AC 驗證。
詳細格式見 `.claude/agents/pm.md` 的「Output Formats > Validation Report」段。

執行流程：
1. 找到 task_id 對應的 Task Specification（確認 AC 清單）
2. 找到對應 agent 的最新輸出
3. 逐條核查每條 AC（binary PASS / FAIL）
4. 依 `.claude/agents/pm.md` 的 Validation Report 模板輸出（=== 格式）

若未提供 task_id，列出目前所有 status=done 且等待 validation 的任務，由操作者選擇。

遵守規則：
- PRINCIPLE-04：驗證是二元的，無「部分通過」
- RULE-PM-05：有任何 AC 未測試就 APPROVE = 違規
