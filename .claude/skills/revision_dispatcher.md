# Skill: revision_dispatcher
# Owner: PM | 用途：撰寫明確指出 AC 失敗的 Revision Request

---

## 目的

當 Validation Report 的 OVERALL = FAIL 時，產出一份有效的 Revision Request，明確告訴 agent 哪裡出了問題、預期是什麼、以及不要犯什麼常見錯誤。

---

## Revision Request 品質標準

### 有效的 Revision Request
```
Failing criteria: AC3, AC5
Issue: AC3 — whitespace 陣列有 3 個條目，但其中 2 個的 evidence 欄位為空字串 ""。
       AC5 — differentiation_angles 包含 "提供更好的客戶服務" 這類泛用建議，
              未基於 whitespace 分析中的具體 gaps。
Expected: AC3 — 每個 whitespace 條目的 evidence 欄位必須說明這個 gap 的具體來源，
                例如「G2 上競品 X 有 47 條評論抱怨缺乏 API 整合能力」。
          AC5 — differentiation_angles 必須直接引用 whitespace 中找到的具體 gap，
                例如「利用競品普遍缺乏無程式碼設定流程的 whitespace」。
Do NOT: 不要把 evidence 欄位填入 "See above" 或 "Based on analysis"，
        這類非具體描述仍然會 FAIL AC3。
```

### 無效的 Revision Request（PM 違規）
```
✗ "請提升輸出品質" — 沒有引用 AC 編號，無效
✗ "JSON 有問題，請修正" — 沒有說明具體是什麼問題
✗ "我幫你改了 AC3 的部分，請看看" — PM 不得修改員工輸出
✗ "AC3 和 AC5 都過不了，你懂我的意思" — 沒有說明 expected output
```

---

## Revision Request 模板

```
REVISION_REQUEST:
  Failing criteria: [AC 編號列表，用逗號分隔]

  Issue(s):
    [AC 編號]: [具體說明為什麼這條 AC 沒通過，引用員工輸出中的具體內容]
    [AC 編號]: [同上]

  Expected:
    [AC 編號]: [修正後的輸出必須包含什麼，越具體越好]
    [AC 編號]: [同上]

  Do NOT:
    - [針對此次失敗，列出 agent 在修正時容易再犯的錯誤]
    - [如果適用，說明一個常見的誤解]

  Reference:
    [若相關，引用適用的 PRD 約束或 phase-gate 條件]
```

---

## 各 Phase 常見 Revision 觸發點

### Phase 1 — E2 Competitor Analyst
```
最常見 FAIL: whitespace.evidence 為空
Revision: 要求為每個 whitespace gap 提供具體來源
          （具體說：哪個平台的評論、什麼投訴、哪個功能缺口）
Do NOT: 不要把 opportunity 欄位的內容複製到 evidence 欄位
```

### Phase 2 — E4 Product Strategist
```
最常見 FAIL: must_have > 8 個功能，或 AC 是主觀描述
Revision: 要求移除或降級超出的功能，重寫 AC 為 binary testable
Do NOT: 不要把「功能描述」直接當 AC
```

### Phase 3 — E5 Product Designer
```
最常見 FAIL: user_flow step 引用不存在的 component，或缺少 error_states
Revision: 要求對齊 user_flow 引用的 component 名稱，補充缺少的 error_states
Do NOT: 不要把 error state 寫在 user_flow 的 step 裡當作補充
```

### Phase 4 — E6/E7 Engineer
```
最常見 FAIL: console.log / hardcoded secrets
Revision: 要求 grep 整個 codebase 確認無此模式，重新提交
Do NOT: 不要只移除「看得到的」console.log，要確保所有環境都清除
```

### Phase 5 — E8 QA Engineer
```
最常見 FAIL: 某個 must_have 功能少於 3 個 test cases，或缺少 security tests
Revision: 要求為缺少的功能補充 test cases，安全測試最低覆蓋 4 種攻擊
Do NOT: 不要把 negative test 和 edge case test 合併算作兩個
```

---

## Revision 計數追蹤

PM 應追蹤每個 agent 的 revision 次數：
- 第 1 次 revision：正常流程
- 第 2 次 revision：在 revision request 中加強說明，並加入 "此次是第 2 次修改請求"
- 第 3 次 revision：上報人工操作者，標記該 agent 可能需要 system prompt 調整或由 HR 重新 onboard
