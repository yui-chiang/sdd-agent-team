# Skill: validation_engine
# Owner: PM | 用途：對每條 AC 執行 binary pass/fail 驗證

---

## 目的

對 agent 的輸出逐條檢查 Task Specification 中的每條 Acceptance Criteria，產出 PASS/FAIL 的二元結果，不允許「部分通過」。

---

## 執行步驟

```
Step 1: 取得對應的 Task Specification（確認 TASK_ID 匹配）
Step 2: 逐條讀取 ACCEPTANCE_CRITERIA
Step 3: 對照員工輸出，逐條判斷
Step 4: 任何 FAIL → OVERALL = FAIL，整個 output 需要 revision
Step 5: 全部 PASS → OVERALL = PASS，output 可以 APPROVED
Step 6: 輸出 Validation Report
```

---

## 驗證規則

### Binary 規則
- AC 的結果只有兩個值：`PASS` 或 `FAIL`
- 沒有 `PARTIAL PASS`、`MOSTLY OK`、`MINOR ISSUE`
- 即使只有一條 AC FAIL，OVERALL 就是 FAIL

### 測試什麼、不測試什麼
```
測試（AC 中明確要求的）:
  ✓ JSON 格式正確性
  ✓ 必要欄位存在性
  ✓ 欄位值的合法性（enum 值是否在範圍內）
  ✓ 陣列長度限制（e.g. must_have ≤ 8）
  ✓ 跨欄位一致性（e.g. feature_refs 對應真實 feature ID）
  ✓ 禁止項不存在（e.g. console.log, hardcoded secrets）

不測試（非 AC 範疇）:
  ✗ 輸出「夠不夠好」的主觀判斷
  ✗ AC 以外的內容品質
  ✗ PM 認為「應該有」但未列入 AC 的項目
```

---

## Validation Report 格式

```
===
TASK_ID    : [匹配原 spec 的 TASK_ID]
VALIDATOR  : PM
TIMESTAMP  : [YYYY-MM-DD HH:MM]
OVERALL    : PASS | FAIL
RESULTS:
  AC1: PASS | FAIL — [若 FAIL，一行說明原因]
  AC2: PASS | FAIL — [若 FAIL，一行說明原因]
  AC3: PASS | FAIL — [若 FAIL，一行說明原因]
  ...
DECISION   : APPROVED | REVISION_REQUIRED
REVISION_REQUEST:
  [只在 REVISION_REQUIRED 時填寫]
  Failing criteria: [列出 AC 編號]
  Issue: [具體說明問題]
  Expected: [修正後的輸出必須包含什麼]
  Do NOT: [避免在修正中犯的常見錯誤]
===
```

---

## 常見 Validation 錯誤（PM 違規）

```
✗ 「輸出整體不錯，雖然 AC3 沒過，但可以先過」— 違反 binary 原則
✗ 「我幫你把這段改一下就好了」— PM 不得修改員工輸出
✗ 「AC 沒有寫到這個，但我覺得應該要有」— 只測試 AC 中列出的項目
✗ 「時間緊，先 APPROVED 後面再補」— ADR-S003 禁止
✗ 發了 Revision Request 但沒有引用具體 AC 編號 — 無效的 revision request
```

---

## 特殊情況處理

### 輸出格式錯誤（非 JSON）
```
若員工輸出不是合法 JSON（當 JSON 為要求格式時）:
→ AC1（格式驗證）= FAIL
→ OVERALL = FAIL
→ 不需要繼續驗證其他 AC
→ Revision Request: 要求重新提交合法 JSON
```

### 輸出部分缺失
```
若員工輸出缺少整個 section（e.g. CompetitorReport 缺少 whitespace）:
→ 相關 AC = FAIL
→ OVERALL = FAIL
→ Revision Request 明確說明缺少哪個 section 及其要求內容
```

### 邊界值
```
must_have 功能恰好是 8 個 → PASS（≤8 的規則，8 合法）
must_have 功能是 9 個 → FAIL（違反 ADR-S005）
whitespace 有 evidence 欄位但值為空字串 → FAIL（空字串不等於填寫）
```
