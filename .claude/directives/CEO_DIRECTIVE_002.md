---
directive_id: CEO_DIRECTIVE_002
issued_by: CEO
issued_to: PM
date: 2026-04-25
subject: 新增行銷部門（M1–M6）+ Phase 6 Go-to-Market — 公司架構變更
status: ACTIVE
---

# CEO_DIRECTIVE_002
# 新增 Marketing Department（M1–M6）與 Phase 6 持續循環層

---

## 決策摘要

於現有產品開發體系（CEO / PM / HR / E1–E13）之外，新增 **Marketing Department**：

```
M1 Marketing Strategist          (Code: MS)  L2  Phase 6
M2 Content Producer              (Code: CP)  L2  Phase 6
M3 Distribution & Partnerships   (Code: DP)  L2  Phase 6
M4 Paid Acquisition              (Code: PA)  L2  Phase 6
M5 Community & Engagement        (Code: CE)  L2  Phase 6
M6 Growth Analyst                (Code: GA)  L2  Phase 6
```

並新增 **Phase 6 — Go-to-Market**，作為 ship 後的持續循環層（每 cycle 建議 14 天）。

總 agent 數量：14 → 20（含 CEO/PM/HR、E1–E13、M1–M6）。
組織層級不變：CEO → PM → HR + E/M agents（M agents 與 E agents 同層，皆由 PM 協調）。

---

## 決策依據

1. 既有 13 個 agents（E1–E13）的 domain 全部聚焦在「產品從 idea 蓋到 ship」，
   ship 之後的銷售、通路、社群、廣告、客服互動完全沒有任何 agent 涵蓋。
2. CEO 需求：建立完全自主的銷售與行銷團隊，能進行貼文發佈、廣告投放、KOL 通路擴展、
   顧客自動回覆、成效迴圈優化，且 CEO 不需親自管理。
3. 為防止 ship 後「無人管理」造成的品牌風險、預算失控、客戶流失，需在 SDD 體系內
   為行銷層也建立明確的 domain lock 與 phase gate。

---

## CEO 已批准的特別條款

於 2026-04-25 對話中，CEO 已就以下事項做出明確批准，HR 寫入 agent 文件：

### 1. 預算決策權
M4 Paid Acquisition 的日預算上限與 ROAS 紅線由 **PM 拍板**；M4 不得自行調整（RULE-PA-04）。
對應 ADR 更新觸發點：「M4 預算紅線（daily / ROAS / CPA）變更」必須記錄為 ADR entry。

### 2. M5 強制 Escalation 對象
M5 Community & Engagement 遇到投訴 / 退款 / 法律 / 媒體 / 隱私 / 自殺自傷訊息時，
一律 escalate 給 **人類 CEO**，不經 PM、不自動回覆（RULE-CE-01）。
SLA：CRITICAL 30 分鐘 / HIGH 4 小時 / NORMAL 24 小時。
PM 在 onboarding M5 時必須確認 CEO 指定的 human_recipient（email 或 Slack channel）
並寫入 EngagementProtocol.escalation_protocol.mandatory_escalation_categories[*].human_recipient。

### 3. 外部憑證
M3 / M4 / M5 / M6 需要連接 IG / FB / TikTok / 廣告帳戶 / CRM 等外部服務的存取權；
agent 文件中已標註 `required_credentials` 欄位列出所需 token 類型。
所有憑證必須遵守 ADR-S004 精神，由 E8 透過環境變數注入，禁止 hardcode（RULE-MK-04）。

---

## PM 必須執行的更新清單

以下文件必須在此 DIRECTIVE 發出後完成同步：

```
[x] .claude/agents/m1-marketing-strategist.md       ← HR 已新增（2026-04-25）
[x] .claude/agents/m2-content-producer.md           ← HR 已新增
[x] .claude/agents/m3-distribution-partnerships.md  ← HR 已新增
[x] .claude/agents/m4-paid-acquisition.md           ← HR 已新增
[x] .claude/agents/m5-community-engagement.md       ← HR 已新增
[x] .claude/agents/m6-growth-analyst.md             ← HR 已新增
[x] .claude/rules/domain-lock.md                    ← 已加入 M1–M6 主表 + 硬規則 + 快查
[x] .claude/rules/phase-gates.md                    ← 已加入 Phase 6 GATE checklist
[ ] CLAUDE.md                                       ← PM 待更新：agent 名單、Phase 流程、章規
[ ] PRD：建立 Phase 6 對應的 Go-to-Market 章節（建議從 PRD v1.1 起遞增）
[ ] ADR：新增一條 ADR-P6-001 記錄 Phase 6 設立與運作模式
```

---

## Phase 6 運作架構（HR 規格摘要）

```
Phase 6 Cycle 流程（每 14 天為一輪）：

  Day 0       M1 產出/更新 MarketingStrategy
                ↓
  Day 1-2     並行：M2 / M3 / M4 / M5 各自接收 strategy 產出 spec
                ↓
  Day 3-13    執行（外部工具承載：Metricool / Meta Ads / ManyChat 等）
                ↓
  Day 14      M6 產出 GrowthReport，PM 驗證所有 P6-*-001 checklist
                ↓
  Day 14+1    下一 cycle Day 0：M1 依 GrowthReport 調整 strategy
```

Phase 6 與 Phase 1–5 的關鍵差異：

| 項目 | Phase 1–5 | Phase 6 |
|---|---|---|
| 性質 | 一次性 gate | 持續循環 |
| Gate 通過後 | 進入下一 Phase | 進入下一 cycle |
| 失敗影響 | 阻擋下游啟動 | 暫停執行，當期 cycle 重來 |
| ADR 更新頻率 | 事件觸發 | 每 cycle 結束至少一條 |

---

## CLAUDE.md 待更新項目（PM 執行重點）

PM 在更新 CLAUDE.md 時，請新增或修改以下章節：

### 1. 「系統架構總覽」區段
- agents 數量：13 → 20（含 M1–M6）
- 在現有列表後新增：
  ```
  M1 Marketing Strategist          — Phase 6（Go-to-Market 策略源頭）
  M2 Content Producer              — Phase 6（內容生產）
  M3 Distribution & Partnerships   — Phase 6（KOL / 通路擴展）
  M4 Paid Acquisition              — Phase 6（付費投放）
  M5 Community & Engagement        — Phase 6（社群與顧客回覆）
  M6 Growth Analyst                — Phase 6（成效分析迴圈）
  ```

### 2. 「組織層級」區段
- M1–M6 與 E1–E13 同層，皆由 PM 協調
- 無新增管理層級

### 3. 「Phase 流程」區段
- 新增 Phase 6 列：
  ```
  | 6 — Go-to-Market | 持續循環（cycle ≈ 14 天） | M1, M2, M3, M4, M5, M6 |
    MarketingStrategy / ContentBatch / DistributionPlan / AdCampaignSpec /
    EngagementProtocol / GrowthReport |
  ```

### 4. 「不可妥協原則」區段（無新增，但補充說明）
- 所有 5 條既有原則同樣適用於 M-agents
- 特別強調：M4 預算紅線是 RULE-PA-04 的延伸，PM 不得授權給 M4 自行調整

---

## 授權聲明

此決策已由人工操作者（CEO Yui）批准（2026-04-25 對話）。
HR 依此 DIRECTIVE 執行 agent 新增，無需額外 CEO 批准。
PM 負責驗證 HR 輸出，並完成 CLAUDE.md / PRD / ADR 三份文件的同步。

```
Signed: CEO Yui
Date  : 2026-04-25
```
