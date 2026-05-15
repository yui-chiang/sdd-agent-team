# Phase Gate Checklists
# PM 在啟動每個新 Phase 前，必須確認上一個 Phase 的所有 gate items 都已 APPROVED

---

## PHASE 1 GATE — Discovery 完成條件
> 全部打勾後，才可以發出 Phase 2 的第一個 Task Spec

```
[ ] P1-MA-001  MarketReport 已交付並通過 PM Validation（OVERALL: PASS）
[ ] P1-CA-001  CompetitorReport 已交付並通過 PM Validation（OVERALL: PASS）
[ ] P1-DC-001  DomainReport 已交付並通過 PM Validation（OVERALL: PASS）
[ ] ADR 已更新，Phase 1 的關鍵決策已記錄
[ ] PRD v0.2 已發布（加入市場、競品 context）
[ ] DomainReport.mvp_feasibility_verdict ≠ NOT_FEASIBLE（若為 NOT_FEASIBLE，上報人工操作者）
```

**若 DomainReport 有 blockers**：
PM 必須將 `mvp_feasibility_verdict.blockers` 中的每一項加入 PRD 的 open questions，並標記 `blocking: true`，在 Phase 2 開始前須解決。

---

## PHASE 2 GATE — Product Definition 完成條件
> 全部打勾後，才可以發出 Phase 3 的第一個 Task Spec

```
[ ] P2-PS-001  MVPDefinition 已交付並通過 PM Validation（OVERALL: PASS）
[ ] must_have 功能數量 ≤ 8（超過 8 個 = PM 打回，要求 E4 縮減）
[ ] 所有 open_questions.blocking=true 的問題已解決
[ ] 每個 must_have 功能都能追溯到 MarketReport 的 pain point 或 CompetitorReport 的 whitespace
[ ] 每個 acceptance_criteria 是 binary testable（可用 yes/no 回答）
[ ] ADR 已更新
[ ] PRD v0.3 已發布（包含完整 MVP scope）
```

---

## PHASE 3 GATE — Design 完成條件
> 全部打勾後，才可以發出 Phase 4 的第一個 Task Spec
> Phase 3 為兩段串行：E5 Product Designer → E6 UI/UX Designer，兩者皆 APPROVED 才算通過

```
[ ] P3-PD-001  DesignSpec（E5）已交付並通過 PM Validation（OVERALL: PASS）
[ ] 每個 must_have 功能都有對應的 user_flow 條目
[ ] 每個 user_flow 的每個 step 都引用了 components 陣列中真實存在的 component
[ ] 色彩對比度符合 WCAG AA（≥ 4.5:1，文字對比）
[ ] error_states 至少涵蓋：404、network failure、auth failure、form validation
[ ] 所有 components 的 accessibility 欄位已填寫
[ ] P3-UX-001  UISpec（E6）已交付並通過 PM Validation（OVERALL: PASS）
[ ] UISpec.interaction_states 涵蓋所有 DesignSpec.components（無缺漏 component）
[ ] 所有 interactive elements 的 minimum_touch_target ≥ 44×44px
[ ] hover 與 focus 狀態視覺上可區分
[ ] motion_spec 為所有頁面轉場和 component mount/unmount 指定 duration 與 easing
[ ] ADR 已更新（E5 DesignSpec + E6 UISpec 設計決策）
[ ] PRD v0.4 已發布（加入設計決策，含視覺與互動規格摘要）
```

---

## PHASE 4 GATE — Engineering 完成條件
> 全部打勾後，才可以發出 Phase 5 的第一個 Task Spec
> Phase 4 為三方並行：E7（前端）、E8（後端）、E13（AI 工程，若產品含 AI 功能）
> 若產品不含 AI 功能，P4-AIE-001 可標記 NOT_APPLICABLE，無需等待

```
[ ] P4-FE-001  Frontend delivery（E7）已交付並通過 PM Validation（OVERALL: PASS）
[ ] P4-BE-001  Backend delivery（E8）已交付並通過 PM Validation（OVERALL: PASS）
[ ] P4-AIE-001 AI delivery（E13）已交付並通過 PM Validation（OVERALL: PASS）
              （若產品無 AI 功能，標記 NOT_APPLICABLE 並在 ADR 記錄理由）
[ ] E7 FE manifest 的每個 api_contract endpoint 都已在 E8 BE manifest 中實作
[ ] E7 FE 與 E8 BE 的 api_contracts 對齊（request/response shape 一致，無 mismatch）
[ ] E13 AISpec 的 api_contracts 與 E8 BE manifest 對齊（AI 服務介面一致，無 mismatch）
[ ] 安全基線驗證通過（逐項確認）：
    [ ] 無 hardcoded secrets / credentials / tokens（含 LLM API keys）
    [ ] 所有 endpoints 有 input validation
    [ ] SQL 使用 parameterized queries 或 ORM
    [ ] auth endpoints 有 rate limiting
    [ ] CORS 配置為已知 origins
    [ ] 密碼使用 bcrypt（min cost 12）
    [ ] AI input guardrails 已實作（若有 AI 功能）
[ ] 所有 must_have 功能在前後端皆已實作（端到端可執行）
[ ] ADR 已更新（技術決策記錄，含 AI 技術決策）
[ ] PRD v0.5 已發布（加入已知技術限制 known_limitations，含 AI 限制）
```

---

## PHASE 5 GATE — Ship 決策條件
> E8 QA Engineer 的 BugReport 通過後，PM 做最終 ship 決策

```
[ ] P5-QA-001  TestPlan 已交付並通過 PM Validation
[ ] P5-QA-002  BugReport 已交付並通過 PM Validation
[ ] ship_criteria_check.all_critical_resolved = true
    （有任何 CRITICAL bug 未解決 = 強制 DO_NOT_SHIP）
[ ] ship_criteria_check.security_tests_passed = true
[ ] ship_criteria_check.core_user_flows_complete = true
[ ] ship_criteria_check.accessibility_baseline = true
[ ] 所有 bug.blocker=true 的 bug 已 assign 並解決
[ ] ship_recommendation = SHIP 或 SHIP_WITH_FIXES（PM 最終裁決）
[ ] ADR 已更新（ship 決策記錄）
[ ] PRD v1.0 最終版本已發布
```

---

---

## PHASE 6 GATE — Go-to-Market 完成條件
> 此 Phase 為 ship 後的持續循環層；不像 Phase 1–5 是一次性 gate，Phase 6 每 cycle（建議 14 天）
> 結束都需要驗證一次以下 checklist 才能進入下一 cycle
> 由 CEO 批准設立（2026-04-25），參考 CEO_DIRECTIVE_002

```
[ ] P6-MS-001  MarketingStrategy（M1）已交付並通過 PM Validation（OVERALL: PASS）
                positioning + ICP + messaging_pillars + channel_mix 四欄完整
                至少 3 個 messaging pillars
                brand_red_lines 不為空（≥ 3 forbidden_words + ≥ 1 compliance_constraint）
                growth_okr 含可量化 north star + ≥ 1 個 OKR
[ ] P6-CP-001  ContentBatch（M2）已交付並通過 PM Validation（OVERALL: PASS）
                每則貼文都有 pillar_id、compliance_check 三欄全填
                所有 visual_description 不含台詞文字（依用戶記憶 feedback_storyboard_no_text）
                全平台無 emoji（依用戶記憶 feedback_no_emoji）
[ ] P6-DP-001  DistributionPlan（M3）已交付並通過 PM Validation（OVERALL: PASS）
                所有 KOL 通過 red_line_check
                超過 USD 500/月的合作標記 above_cap_requires_pm_approval
                community_placement 全部附 rules_summary
[ ] P6-PA-001  AdCampaignSpec（M4）已交付並通過 PM Validation（OVERALL: PASS）
                每組廣告皆含 daily_budget cap + ROAS red line（雙斷路器）
                所有 ad.creative_ref 對應到 M2 ContentBatch 已存在的素材
                預算紅線由 PM 拍板並記錄 approved_by: PM
[ ] P6-CE-001  EngagementProtocol（M5）已交付並通過 PM Validation（OVERALL: PASS）
                mandatory_escalation_categories 涵蓋：
                  complaint / refund / legal / media / privacy / crisis
                所有 mandatory escalation 對應 human_recipient（人類 CEO email 或指定 channel）
                discount_authorization.approved_codes 全部標明 approved_by: PM
                response_templates 全部符合 M1.tone_of_voice
[ ] P6-GA-001  GrowthReport（M6）已交付並通過 PM Validation（OVERALL: PASS）
                所有 ab_test_results 標明 result_classification（CONCLUSIVE/DIRECTIONAL/INCONCLUSIVE）
                ltv_estimate 附 95% confidence interval
                data_quality_notes 不為空
                next_cycle_recommendations.budget_reallocation_suggestion 標明 decision_required_from
[ ] 跨 agent 一致性：
    [ ] M2 內容對應的 pillar_id 全部存在於 M1.messaging_pillars
    [ ] M4 ad.creative_ref 全部對應 M2 既有素材
    [ ] M5 reply_tree 中所有產品功能承諾對應 MVPDefinition.must_have features
    [ ] M3 KOL 全部通過 M1.brand_red_lines.forbidden_partner_types check
[ ] 安全與合規：
    [ ] 無外部憑證 hardcode（Meta / Google / TikTok / CRM tokens 透過環境變數注入）
    [ ] 無顧客個資（email / 電話 / 姓名）出現在 GrowthReport 或 EngagementProtocol 明文中
    [ ] 強制 escalation 通道在過去 cycle 內可用（CEO email 或 channel 沒被關閉）
[ ] ADR 已更新（含 strategy 變更、預算紅線變更、escalation 規則變更）
[ ] PRD 對應版本已更新（Phase 6 後續版本由 PM 決定，建議從 v1.1 開始遞增）
```

### Phase 6 cycle 流程

```
Cycle Day 0   ── M1 產出/更新 MarketingStrategy
Cycle Day 1-2 ── M2/M3/M4/M5 並行接收 strategy 並產出各自 spec
Cycle Day 3-13── 執行（外部工具承載：Buffer/Metricool/Meta Ads/ManyChat 等）
Cycle Day 14  ── M6 產出 GrowthReport，PM 驗證 P6-*-001 全部 checklist
Cycle Day 14+1── 下一 cycle Day 0：M1 依 GrowthReport 調整 strategy
```

### Phase 6 特別違規處理

```
情況                                      → 處理方式
──────────────────────────────────────────────────
M4 廣告連續 3 天 ROAS < red_line          → 自動 auto-pause；PM 評估是否更換素材或暫停整組
日廣告花費突破 daily_total_cap            → 立即 auto-pause 全部 paid 通路；PM 重新核准才能恢復
M5 收到 mandatory escalation 訊息超過 SLA → E9 Operations Monitor 立即 alert PM；
                                            PM 可暫停 M5 自動回覆，全面轉人
M6 樣本不足 cycle 連續 2 期               → 延長 cycle 至 28 天；M4 維持當期投放不調整受眾
M-agents 之間衝突（如 M2 與 M5 樣板矛盾）  → PM 協調；無共識則上報 CEO
```

## Phase Gate 違規處理

```
情況                              → 處理方式
─────────────────────────────────────────────────
PM 發出 Phase N+1 spec            → 立即撤回 spec，補完 Phase N gate
  而 Phase N 未清
E7/E8 並行中一方 FAIL             → 等待 FAIL 方修改完成，兩者皆 APPROVED 才過 gate
DomainReport 有 NOT_FEASIBLE      → 上報人工操作者，系統暫停
Phase 1 三個 reports 有衝突        → PM 上報人工操作者，不自行裁決
```

---

## ADR 更新觸發點

每當以下事件發生，PM 必須立即新增一條 ADR entry：

- 任何 employee output 被 APPROVED
- 任何 phase gate 被清除（含 Phase 6 每個 cycle 結束）
- 任何 scope 決策（功能新增、移除、延後）
- 任何安全或合規決策
- Ship 決策（SHIP / SHIP_WITH_FIXES / DO_NOT_SHIP）
- Phase 6 cycle 結束時的策略 / 預算 / escalation 規則調整
- M4 預算紅線（daily / ROAS / CPA）變更
- M5 強制 escalation 類別新增或調整
