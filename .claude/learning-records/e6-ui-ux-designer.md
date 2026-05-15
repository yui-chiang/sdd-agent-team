# E6 UI/UX Designer — Skill Growth Archive
**Maintained by:** HR（根據每日 PM-VERIFIED 條目更新）
**Last Updated:** 2026-04-05
**Domain Focus:** Visual design systems, interaction design, motion spec, dark-mode UI, dashboard UX, cinematic-ui workflow, WCAG 2.1 AA visual compliance

> 📌 每日學習紀錄請見 `daily/YYYY-MM-DD.md`。此檔案只記錄已驗證的累積技能。

---

## Accumulated Skills

<!-- HR 在此處依序新增已驗證的技能條目 -->
- 2026-04-05 **SpriteCook.ai Agent Page — Dark-Theme SaaS Dashboard UX 模式** — SpriteCook.ai (https://www.spritecook.ai/agents)

  **E6 設計學習摘要（來源：spritecook.ai/agents 頁面逆向解析）**

  **色彩系統：** 核心背景色 `#0b1020`（深藍近黑）+ CSS 自訂屬性體系（`--bg-surface`, `--text`, `--text-secondary`, `--text-tertiary`），用於建立嚴格的對比層級，避免了純黑背景過於壓迫的問題，深藍底色讓文字對比更柔和且符合 WCAG AA。

  **三步驟工作流視覺法（Connect → Prompt → Ship）：** 適用於任何需要「引導用戶完成多步驟任務」的 Dashboard 頁面。視覺模式：數字序號 + 短動詞標題 + 單句說明 + 行動按鈕，三個 Step 以水平或垂直排列（依 viewport 而定）。E6 在設計 onboarding flow 或空狀態引導時可直接套用此模式，避免長文字說明。

  **Code Block 的 UI 角色：** 終端機指令（CLI command）在 SaaS 產品中被當作「高信度的設計元素」使用，背景使用更深的 surface 色、等寬字型、無框線圓角容器，表達精確性與技術可信度。對應 E6 域：若 Dashboard 有 API Key、Webhook URL 等展示需求，應使用此 code-block treatment，而非普通 input 框。

  **「Works with」相容性區塊：** Logo grid / 圖示列表水平排列，間距充足，用於傳達生態相容性。與 dark surface 搭配時，logo 需處理成單色或高對比版本，避免彩色 logo 在深背景上對比不足（WCAG AA 風險）。

  **CTAs 層級設計：** Primary CTA（高對比填色按鈕）→ Secondary CTA（outline 或 ghost 按鈕）嚴格區分，never 兩個相同視覺權重的按鈕並排。SpriteCook 的 `btn btn-primary` 類別命名策略對應 E6 在 UISpec 的 `component_visual_refinements` 中明確標注按鈕層級。

  **Anti-Pattern（從 SpriteCook 逆向學習避免）：** 該頁面 hero 使用「主標題 + 描述文字 + CTA」的對稱置中佈局，為成熟但「可預期」的 SaaS 模板式構成。E6 在設計主視覺時應根據 cinematic-ui 的 Anti-Convergence 原則，若採用此構成則必須在其他 section 引入不對稱或 editorial 排版以增加視覺辨識度，避免整站同質化。

  **Relevance（E6 適用時機）：** 當 UISpec 中需要設計 Dashboard onboarding、Agent 狀態卡、Workflow 可視化、API Key 展示等元件時，可直接套用上述設計決策。特別適用於本系統的 Agent Roster Dashboard 設計任務。
