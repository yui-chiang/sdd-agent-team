# Global Rules — MVP Build Multi-Agent System
# 適用範圍：所有專案、所有 agents、所有 phases

---

## 系統不可妥協原則

### PRINCIPLE-01：Domain Lock 絕對執行
每個 agent 只能產出其被定義的 domain 內容。
超出 domain 的任何輸出 = 立即違規 → PM 拒絕並重新 assign → 原 agent 不得抗議。
「我只是順帶補充」不是合法理由。

### PRINCIPLE-02：PM 永不產出內容
PM 的合法輸出只有三種：
1. Task Specification
2. Validation Report
3. PRD update

PM 若產出 code、design、research findings、test cases = 系統錯誤，需立即回退。

### PRINCIPLE-03：Phase Gate 強制執行
Phase N+1 的任何 Task Spec 不得在 Phase N 所有任務 APPROVED 之前發出。
並行任務（Phase 1 的 E1/E2/E3，Phase 4 的 E7/E8/E13）必須全部 APPROVED 才算 Phase 通過。
「先做好的先進」不適用於 Phase Gate。

### PRINCIPLE-04：驗證是二元的
AC（Acceptance Criteria）的驗證結果只有：
- `PASS` — 完全符合，無例外
- `FAIL` — 未達標，需 revision

沒有「90% 通過」、「大致符合」、「算了先過」。
「時間緊」不是降低標準的理由。

---

## 全域輸出格式規則

### 所有 Employee Agent 的 JSON 輸出
- 輸出必須是合法 JSON（可通過 `JSON.parse` 驗證）
- 不得有 Markdown fence（```json ... ```）包裹 JSON
- 不得有 preamble（「好的，以下是我的輸出：」等前言）
- 不得有 commentary（輸出後的補充說明）
- 若無法填入某欄位，使用 `"INSUFFICIENT_DATA"`，並加入對應的 `data_gaps` 或 `known_limitations`

### PM 的文件輸出
- Task Specification 使用規定的 `===` 格式（見 `agents/pm.md`）
- Validation Report 使用規定的 `===` 格式
- PRD Update 使用規定的 `## PRD v[n]` 格式

---

## Task ID 命名規範

```
格式：[PHASE_CODE]-[AGENT_CODE]-[SEQ]
範例：P1-MA-001, P1-ARCH-001, P2-PS-001, P4-FE-001

PHASE_CODE：P1 / P2 / P3 / P4 / P5
AGENT_CODE：MA / CA / DC / PS / PD / UX / FE / BE / QA / ARCH / DEVOPS / AIE
SEQ       ：三位數，從 001 開始
```

---

## 衝突解決規則

| 衝突類型 | 處理方式 |
|---|---|
| 兩個 agent 輸出相互矛盾 | PM 上報人工操作者，PM 不自行裁決 |
| Agent 認為 PM spec 有誤 | Agent 可在輸出中的 `known_limitations` 標記，但先按 spec 執行 |
| Phase Gate 未清但時間壓力大 | 強制等待，不可跳過 |

---

## 安全基線（所有工程輸出強制適用）

以下任一項缺失 = 自動 FAIL，不論其他品質多高：

- [ ] 沒有 hardcoded secrets / credentials / tokens
- [ ] 所有 API endpoints 有 input validation（400 for malformed requests）
- [ ] SQL 使用 parameterized queries 或 ORM（禁止字串拼接）
- [ ] Auth endpoints 有 rate limiting
- [ ] CORS 限制為已知 origins
- [ ] 密碼使用 bcrypt（min cost 12）
- [ ] Token 使用 HTTP-only cookies（瀏覽器 client）

---

## PRD 版本管理

| PRD 版本 | 更新時機 |
|---|---|
| v0.1 | PM 收到 product idea，初始化 PRD |
| v0.2 | Phase 1 APPROVED 後，加入市場 + 競品 context 及技術架構可行性 context（E12 ArchSpec 摘要） |
| v0.3 | Phase 2 APPROVED 後，加入完整 MVP scope |
| v0.4 | Phase 3 APPROVED 後，加入設計決策 |
| v0.5 | Phase 4 APPROVED 後，加入已知技術限制 |
| v1.0 | Phase 5 APPROVED，最終版本，標記 ship 決策 |
