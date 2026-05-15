---
skill_id: superpowers:write-plan
name: write-plan
source: github.com/obra/superpowers (adapted for MVP Build Multi-Agent System)
authorized_agents: [E7, E8]
phase: 4
---

# superpowers:write-plan
# 授權使用者：E7 Frontend Engineer、E8 Backend Engineer
# 禁止使用者：所有其他 agents

---

## ⚠️ Domain Lock 警告

此 skill 授權給 E7/E8 用於**工程實作計畫的撰寫**。

- 計畫內容必須以 UISpec（E7）或 MVPDefinition+DomainReport（E8）為依據
- 計畫不可新增、修改或刪除 MVPDefinition.must_have 功能
- 計畫中的 API 設計變更必須在 Delivery Manifest 的 `known_limitations` 中標記，並通知 PM

---

## 使用時機

在 `superpowers:brainstorm` 技術設計確認後，或 PM 發出 Task Spec 後，開始撰寫逐步實作計畫。

**觸發條件：**
- 收到 PM 的 Task Spec（P4-FE-xxx 或 P4-BE-xxx）
- `superpowers:brainstorm` 完成並已確認技術方向
- 實作任務包含 3 個以上步驟

---

## 流程

```
1. 宣告：「我正在使用 superpowers:write-plan 建立實作計畫。」
2. 確認 scope：讀取 Task Spec，檢查對應的 UISpec / MVPDefinition / DomainReport 段落
3. 確認 Scope 邊界：若任務涉及多個獨立子系統，建議拆分為多個子計畫
4. 規劃檔案結構：列出所有要新增或修改的檔案，說明每個檔案的職責
5. 撰寫逐步任務（每步 2–5 分鐘），格式如下
6. 計畫自我審查（見下方 Checklist）
7. 儲存計畫至 docs/superpowers/plans/YYYY-MM-DD-<feature>.md 並 commit
8. 詢問執行方式後銜接 superpowers:execute-plan
```

---

## 計畫文件格式

```markdown
# [功能名稱] 實作計畫

> **For agentic workers:** 使用 superpowers:execute-plan 執行此計畫。

**Goal:** [一句話描述目標]
**Architecture:** [2–3 句說明方法]
**Tech Stack:** [關鍵技術/框架]

---

### Task N: [元件/模組名稱]

**Files:**
- Create: `exact/path/to/file.ts`
- Modify: `exact/path/to/existing.ts:123-145`
- Test: `tests/exact/path/to/test.ts`

- [ ] **Step 1: 撰寫 failing test**
  ```typescript
  test('描述具體行為', () => {
    // 完整測試程式碼，不使用 TBD
  });
  ```

- [ ] **Step 2: 執行測試確認失敗**
  Run: `npm test path/to/test.ts`
  Expected: FAIL — "function not defined"

- [ ] **Step 3: 寫最小實作**
  ```typescript
  // 完整實作程式碼
  ```

- [ ] **Step 4: 執行測試確認通過**
  Run: `npm test path/to/test.ts`
  Expected: PASS

- [ ] **Step 5: Commit**
  ```bash
  git add <files>
  git commit -m "feat: <具體描述>"
  ```
```

---

## 計畫自我審查 Checklist（寫完計畫後自行執行）

```
[ ] Spec coverage：每個 Task Spec 的 AC 都能在計畫中找到對應的 task
[ ] Placeholder scan：無 TBD、TODO、「稍後補充」、「類似 Task N」
[ ] Type consistency：Task 3 定義的 function 名稱在 Task 7 是否一致
[ ] 所有 code steps 包含完整程式碼（不得只描述「要做什麼」）
[ ] 每個 step 都有明確的驗證指令和預期結果
[ ] 安全基線：無 hardcoded secrets，所有 API endpoints 有 input validation
```

---

## 原則

- **No Placeholders**：每個步驟都包含實際可執行的 code 和指令
- **DRY / YAGNI / TDD**：測試先行，只實作 spec 要求的功能
- **Frequent Commits**：每個完成的邏輯單元都應有一個 commit
- **完整路徑**：所有檔案路徑必須是從 project root 開始的完整路徑
