---
skill_id: superpowers:brainstorm
name: brainstorm
source: github.com/obra/superpowers (adapted for MVP Build Multi-Agent System)
authorized_agents: [E7, E8]
phase: 4
---

# superpowers:brainstorm
# 授權使用者：E7 Frontend Engineer、E8 Backend Engineer（僅限工程層 scope）
# 禁止使用者：所有其他 agents（PM、E1–E6、E10–E13、HR、CEO）

---

## ⚠️ Domain Lock 警告

此 skill 授權給 E7/E8 使用，**僅限於工程實作層的技術設計討論**。

- E7 使用此 skill = 討論 frontend 元件架構、component 拆分方式、狀態管理方案等技術選型
- E8 使用此 skill = 討論 API 設計、DB schema 結構、認證方案等後端技術選型
- **任何涉及產品功能 scope、視覺設計決策、市場策略的「brainstorm」= Domain 違規，立即停止**
- 此 skill **不授權**改變 MVPDefinition 的 must_have 清單

---

## 使用時機

在 Phase 4 開始寫 code 之前，若工程任務有多個技術實作方向待確認，呼叫此 skill 釐清設計再動手。

**觸發條件：**
- Task Spec 中技術選型不明確（例如：state management 方案未定）
- 有 2 種以上實作路徑，不確定哪種更符合 UISpec/MVPDefinition
- 元件拆分或 API 路由結構需要在執行前先確認

---

## 流程

```
1. 確認工程 scope（讀取 Task Spec、UISpec/MVPDefinition 對應段落）
2. 評估背景脈絡（既有 codebase 結構、已定 tech stack）
3. 一次提出一個釐清問題（不要一次問多個）
4. 提出 2–3 個技術方向，含各自 trade-offs 與推薦選項
5. 以段落呈現技術設計，獲得確認後才進入 superpowers:write-plan
```

**結束條件：** 技術設計被確認 → 自動銜接 `superpowers:write-plan`

---

## 輸出規則

- 此 skill 不產出任何 code
- 設計文件存放路徑：`docs/superpowers/specs/YYYY-MM-DD-<feature>-design.md`
- 完成後 commit design doc，再呼叫 `superpowers:write-plan`

---

## 原則

- YAGNI：移除設計中所有「可能有用」但 MVP 不需要的功能
- 一次只問一個問題
- 先提出推薦方向再討論替代方案
- 設計文件需通過自我審查（無 TBD、無內部矛盾、無模糊需求）後才提交
