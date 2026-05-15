---
skill_id: superpowers:execute-plan
name: execute-plan
source: github.com/obra/superpowers (adapted for MVP Build Multi-Agent System)
authorized_agents: [E7, E8]
phase: 4
---

# superpowers:execute-plan
# 授權使用者：E7 Frontend Engineer、E8 Backend Engineer
# 禁止使用者：所有其他 agents

---

## ⚠️ Domain Lock 警告

此 skill 授權給 E7/E8 用於**執行已核准的工程實作計畫**。

- 只能執行由 `superpowers:write-plan` 產出、存放於 `docs/superpowers/plans/` 的計畫文件
- 執行中不可自行修改 Plan 的 scope（發現 spec 問題 → 停止 → 向 PM 回報）
- E7 執行中不得產出任何 backend code；E8 執行中不得產出任何 frontend/UI code

---

## 使用時機

收到 `superpowers:write-plan` 完成並儲存的計畫文件後，開始逐步執行。

**觸發條件：**
- `superpowers:write-plan` 計畫文件已存在且通過自我審查
- PM 未提出計畫層級的異議

---

## 流程

```
1. 宣告：「我正在使用 superpowers:execute-plan 執行實作計畫。」
2. 讀取計畫文件（docs/superpowers/plans/YYYY-MM-DD-<feature>.md）
3. 批判性審查計畫：
   - 有疑問或發現問題 → 在開始執行前向 PM 回報
   - 無疑問 → 建立 TodoWrite 清單，開始執行
4. 逐步執行每個 Task：
   - 標記為 in_progress
   - 嚴格按照 Plan 的每個 step 執行（不跳過、不簡化）
   - 執行各步驟的驗證指令，確認結果符合預期
   - 標記為 completed
5. 所有 Tasks 完成後，執行最終驗證（見下方 Checklist）
6. 產出 Delivery Manifest（符合 E7/E8 各自的 manifest schema）
```

---

## 遭遇阻塞時的處理規則

```
立即停止執行的情況：
✗ 缺少依賴（套件、環境變數、API endpoint 未實作）
✗ 測試持續失敗且原因不明
✗ Plan 中有無法執行的指令
✗ 發現 Plan 與 UISpec/MVPDefinition 有衝突

處理方式：
1. 停止目前 task，保持 in_progress 狀態
2. 向 PM 回報阻塞原因（使用 Self-Report SOP 的 block 指令）
3. 等待 PM 指示，不自行猜測解法
```

---

## 最終驗證 Checklist（所有 Tasks 完成後）

```
[ ] 所有 Plan 中的 checkbox 都已勾選完成
[ ] 所有測試通過（npm test 或對應指令）
[ ] 無 console.log() 在提交的 code 中（E7）
[ ] 安全基線通過（無 hardcoded secrets、input validation 到位）（E7/E8）
[ ] Delivery Manifest 已填寫完整（files、api_contracts、known_limitations）
[ ] 所有 MVPDefinition.must_have 功能都有端到端實作
[ ] Git history 乾淨（每個 task 都有對應 commit）
```

---

## 計畫偏差處理

| 情況 | 處理方式 |
|---|---|
| Plan step 與實際環境不符 | 停止 → 向 PM 回報，等待 Plan 更新 |
| 發現更好的實作方式 | 在 known_limitations 標記，按原 Plan 執行，完成後向 PM 建議 |
| 測試驗證失敗 | 修正 code，不修改測試；若無法修正則向 PM 回報 |
| 安全違規自動偵測 | 立即停止，不提交，回報 PM |

---

## 原則

- **嚴格按 Plan 執行**：不跳步驟，不簡化驗證
- **阻塞時停止，不猜測**：猜測會導致更大的下游錯誤
- **每完成一個 Task 就 commit**：保持 git history 可追溯
- **Domain Lock 優先於進度**：不因「剛好需要」就寫出 domain 外的 code
