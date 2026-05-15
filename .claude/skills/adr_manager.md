# Skill: adr_manager
# Owner: PM | 用途：建立、讀取、更新、交叉引用 ADR 條目

---

## 目的

確保所有重要決策都記錄在 ADR（Architecture Decision Record）中，作為系統唯一的決策依據，防止決策遺忘或被重複辯論。

---

## ADR Entry 格式

```
ADR-[PREFIX][NUMBER]
Date     : [YYYY-MM-DD]
Task Ref : [TASK_ID 或 SYSTEM]
Decision : [決策內容，一句話]
Rationale: [為什麼這樣決定，最多兩句]
Impact   : [這個決策對下游的約束或啟用效果]
Status   : ACTIVE | SUPERSEDED BY ADR-[n]
```

---

## ADR 編號規則

```
ADR-S001 ~ ADR-S005 : 系統預置 Standing Decisions（見 rules/adr-standing.md）
ADR-P1-001+         : Phase 1 決策（E1/E2/E3 產出的關鍵決策）
ADR-P2-001+         : Phase 2 決策（E4 產出的產品決策）
ADR-P3-001+         : Phase 3 決策（E5 的設計決策）
ADR-P4-001+         : Phase 4 決策（E6/E7 的技術決策）
ADR-P4-HR-001+      : HR 新增 agent 決策
ADR-P5-001+         : Phase 5 決策（QA 的 ship 決策）
```

---

## 何時必須新增 ADR

PM 在以下情況必須立即新增 ADR entry：

| 觸發情況 | ADR 範例 |
|---|---|
| Phase 1 任何 agent output APPROVED | "決定採用 bottom-up 方法估算 TAM" |
| E2 確認某個 whitespace gap 值得追求 | "決定以 X gap 作為差異化定位" |
| E3 的可行性裁定 | "決定 MVP 在現有約束下可行，但需解決 3 個 blockers" |
| E4 的功能清單確定 | "決定 MVP must-have 功能為以下 6 項" |
| E5 的設計系統確定 | "決定使用 Inter 作為主字型，採用 sidebar navigation" |
| E6/E7 的技術選型 | "決定前端使用 Next.js + Tailwind，後端使用 Fastify + PostgreSQL" |
| E8 的 ship 決策 | "決定以 SHIP_WITH_FIXES 發布，3 個 HIGH bugs 在 patch 中修復" |
| HR 批准新增 agent | "批准新增 Data Analyst agent，由 HR 完成 onboarding" |
| CEO 指令導致策略轉向 | "依 CEO 指令將 target persona 從 SMB 調整為 Enterprise" |
| 任何 phase gate 清除 | "Phase 1 Gate 清除，所有 Discovery 輸出已 APPROVED" |

---

## ADR 更新（Supersede）

當某個決策被新決策取代：

```
新 ADR 範例：
ADR-P4-003
Date     : 2024-01-15
Task Ref : P4-BE-001-R2
Decision : 改用 Supabase Auth 替代自建 JWT，以符合 DomainReport 的 GDPR 建議。
Rationale: 自建 JWT 無法在 MVP 期間達到 GDPR 要求的資料主權控制。
           Supabase Auth 提供現成的 GDPR 合規工具。
Impact   : E6 必須更新 api_contracts 中的 auth 相關 endpoints，E7 移除 JWT 實作。
Status   : ACTIVE

同時更新舊 ADR：
ADR-P4-001 → Status: SUPERSEDED BY ADR-P4-003
```

---

## ADR 查詢方式

### 查詢某個決策是否已做
```
問題：「我們決定用什麼資料庫？」
查詢：搜尋 ADR-P4- 系列中包含 "database" 的條目
若無對應 ADR → 這個決策尚未做，不得以「大家都知道」行事
```

### 查詢某個 Phase 的所有決策
```
搜尋 ADR-[Phase Prefix]- 的所有條目
確認所有 Status = ACTIVE 的條目仍然有效
```

### 確認 ADR 衝突
```
若兩條 ADR 互相矛盾，且都是 ACTIVE：
→ PM 上報人工操作者
→ 不得自行解決
→ 在 PRD.open_questions 加入此衝突作為 blocking question
```

---

## ADR 範例庫

```
ADR-P1-001
Date     : 2024-01-10
Task Ref : P1-MA-001
Decision : TAM 估算採用 bottom-up 方法，基於可識別的企業用戶數量計算，信心水準 MEDIUM。
Rationale: 此市場缺乏現成的研究報告，top-down 數字誤差過大。
Impact   : E4 在設計 success metrics 時需考慮保守的 SOM 假設。
Status   : ACTIVE

ADR-P2-001
Date     : 2024-01-12
Task Ref : P2-PS-001
Decision : MVP must_have 功能確定為 6 項（F-001 至 F-006），符合 ≤8 上限。
Rationale: 3 個原始 should_have 功能因 domain 技術約束降級至 out_of_scope。
Impact   : E5 只需設計這 6 個功能的 user flows，E6/E7 實作範圍以此為準。
Status   : ACTIVE

ADR-P4-001
Date     : 2024-01-18
Task Ref : P4-FE-001
Decision : 前端採用 Next.js 14 (App Router) + TypeScript + Tailwind CSS。
Rationale: E6 評估後認為 Next.js 的 SSR 能力符合 DomainReport 的 SEO 約束。
Impact   : 所有前端 components 使用 React Server Components，狀態管理使用 Zustand。
Status   : ACTIVE
```
