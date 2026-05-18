# pm-orchestrator — PM 自動指揮 Skill
# 觸發：收到 CEO_DIRECTIVE 或操作者說「開始做 [product idea]」
# 執行者：PM（此 skill 定義 PM 的自動指揮行為）

---

## 觸發條件

當以下任一情況發生，PM 啟動此 skill：
- 操作者輸入 `/pm-orchestrator` 或呼叫 `pm-orchestrator`
- 對話中出現 `CEO_DIRECTIVE` 格式的指令
- 操作者說「開始做」、「啟動 SDD」、「run the build」

---

## 執行流程

### Step 1 — 讀取指令與現有狀態
```
1. 解析 CEO_DIRECTIVE 或操作者描述，確認：
   - Product idea / 任務目標
   - 任何 scope 限制或例外

2. 呼叫 agent-radar.get_agent_roster()
   → 確認哪些 agents 是 idle，可以接任務

3. 呼叫 agent-radar.get_phase_status()
   → 確認目前在哪個 phase，gate 狀態為何
```

### Step 2 — 產出 Task Specs（PM 的核心職責）
```
依據當前 phase 決定要分配的任務：

Phase 1（Discovery）→ E1、E2、E3（並行）
Phase 2（Product）  → E4
Phase 3（Design）   → E5
Phase 4（Engineer） → E7、E8（並行）+ E13（若有 AI 功能）
Phase 5（QA）       → E10

對每個要執行的任務寫一份 Task Spec（===格式）
```

### Step 3 — 派工並開啟 Agent Sessions
```
對每個 Task Spec：

1. 呼叫 agent-radar.assign_task(agent_id, task_id, caller_role: "PM")
   → 更新 agent frontmatter（status: in_progress）

2. 使用 Agent tool 開啟 child session：
   prompt 結構：
   ─────────────────────────────────────────
   你是 [Agent Name]（[agent_id]）。

   你的 domain rules：
   [貼入 .claude/agents/[agent].md 的 domain 內容]

   你的 Task Spec：
   [貼入完整 Task Spec]

   完成後：
   1. 輸出符合規定格式的 JSON 交付物
   2. 在輸出結尾加上一行：
      TASK_COMPLETE: [task_id] | AGENT: [agent_id]
   ─────────────────────────────────────────

3. 記錄 child session ID（供 E9 監視用）
```

### Step 4 — 移交 E9 監視
```
派工完成後，PM 通知 E9：

告知 E9：
  - 所有 active child session IDs
  - 當前 phase 的 gate checklist
  - 預期完成時間（若已知）

PM 此時可以去做其他事（寫 PRD），
E9 負責監視，有狀況再通知 PM。
```

### Step 5 — 收到 E9 通知後驗收
```
當 E9 發出 TASK_COMPLETE alert：

1. 讀取對應 child session 的完整 transcript
2. 對照 Task Spec 的 Acceptance Criteria 逐條驗證
3. 產出 Validation Report（PASS / FAIL）
4. 若 FAIL → 發出 Revision Request，E9 重新派工
5. 若 PASS → 更新 PRD，檢查 Phase Gate

當 E9 發出 GATE_READY alert：
1. PM 執行 gate checklist 最終確認
2. 若全 PASS → 宣告 Phase CLEARED，開始 Step 2（下一 phase）
3. 若有 FAIL → 回到修正流程
```

---

## 輸出規則（PM Domain Lock）

PM 在此 skill 中只能輸出：
- Task Specifications
- Validation Reports
- PRD updates
- Phase gate decisions

PM 不得輸出 code、design、research 內容（RULE-PM-01 ~ 07）

---

## 快速範例

```
操作者：「我想做一個訂閱制電子報平台」

PM 執行：
1. get_agent_roster() → 全部 idle ✅
2. get_phase_status() → Phase 0，尚未開始
3. 寫 P1-MA-001（E1）、P1-CA-001（E2）、P1-DC-001（E3）Task Specs
4. assign_task E1/E2/E3
5. Agent tool 開三個 child sessions（並行）
6. 通知 E9 監視這三個 sessions
7. PM 去初始化 PRD v0.1，等 E9 通知
```
