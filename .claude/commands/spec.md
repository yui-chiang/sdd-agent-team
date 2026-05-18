# /spec — PM 產出 Task Specification
# 用法：/spec [phase] [agent_code]
# 範例：/spec P4 FE  → 產出 P4-FE-001 的 Task Spec
#       /spec P1 MA  → 產出 P1-MA-001 的 Task Spec
# 執行者：PM

依 $ARGUMENTS 指定的 Phase 和 Agent Code，PM 產出完整 Task Specification。
詳細格式與工作流見 `.claude/agents/pm.md` 的「Output Formats > Task Specification」段。

執行流程：
1. 解析 $ARGUMENTS 取得 phase（P1-P5）和 agent_code（MA/CA/DC/PS/PD/UX/FE/BE/QA/AIE/ARCH/DEVOPS/OPS）
2. 自動產生 task_id（格式：[PHASE_CODE]-[AGENT_CODE]-[SEQ]）
3. 依 `.claude/agents/pm.md` 的 Task Specification 模板輸出（=== 格式）
4. 帶入該 agent 的 domain lock 提醒（見 `.claude/rules/domain-lock.md`）

若未提供參數，列出當前 phase 可以執行的任務，由操作者選擇。

遵守規則：
- RULE-PM-01：PM 不寫 code，只寫 spec
- PRINCIPLE-03：Phase N+1 的 spec 不得在 Phase N gate 未清前發出
- 每條 AC 必須是 binary testable（yes/no 可回答）
- spec 完成後提醒：「記得在下次 phase gate 時同步更新 PRD 版本」
