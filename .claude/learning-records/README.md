# Daily Learning Records

**系統：MVP Build Multi-Agent System — 員工每日學習紀錄**
**發佈人：CEO**
**生效日期：2026-04-03**

---

## 目錄結構

```
.claude/learning-records/
├── README.md                  ← 本文件（系統規則）
├── daily/
│   ├── 2026-04-03.md          ← 每天一份，所有人的學習條目都在這裡
│   └── TEMPLATE.md            ← 每日報告模板（參考用）
├── e1-market-analyst.md       ← HR 維護的長期技能累積檔案
├── e2-competitor-analyst.md
├── e3-domain-consultant.md
├── e4-product-strategist.md
├── e5-product-designer.md
├── e6-frontend-engineer.md
├── e6-ui-ux-designer.md
├── e7-backend-engineer.md
├── e8-qa-engineer.md
├── e9-ops-monitor.md          ← 2026-04-07 加入
├── e11-devops-sre.md          ← 2026-04-07 加入
├── e12-system-architect.md    ← 2026-04-07 加入
├── designer.md                ← 2026-04-07 加入（SkillMap 專屬）
├── pm.md
└── hr.md
```

---

## 工作流程

```
每天 9:00 AM
      │
      ▼
[STEP 1] 所有員工（E1-E8 + E9 + E11 + E12 + Designer + PM + HR）
         各自搜尋一項與自身職能相關的新技術/工具/方法
         將學習紀錄寫入 daily/YYYY-MM-DD.md
         （格式見下方，每人寫自己那一區塊）
      │
      ▼
[STEP 2] PM 審核 daily/YYYY-MM-DD.md
         確認每條紀錄的來源可信度
         在每條紀錄下標記 [PM-VERIFIED ✓] 或 [PM-FLAGGED ⚠️]
         PM 也有自己的學習條目需要完成
      │
      ▼
[STEP 3] PM 通知 HR（在每日報告末尾附上 Summary 給 HR）
      │
      ▼
[STEP 4] HR 讀取 PM Summary
         將 PM-VERIFIED 的新技能
         更新到對應員工的個人技能累積檔案（如 e6-frontend-engineer.md）
```

---

## 每日報告格式（daily/YYYY-MM-DD.md）

每位員工的條目格式如下：

```markdown
### [Employee Name] — [Skill/Topic Title]

**Source:** [完整 URL]
**Author/Publisher:** [姓名 + 平台]
**Credibility Score:** [HIGH / MEDIUM] — [一句理由]
**Summary（中文）：** [2-4 句說明學到什麼，繁體中文]
**Relevance：** [一句話：如何應用在自己的職能上]

> PM Review: [PM-VERIFIED ✓] 或 [PM-FLAGGED ⚠️ — 原因]
```

---

## 可信度判斷標準

| 評級             | 條件                                                                      |
| ---------------- | ------------------------------------------------------------------------- |
| **HIGH**         | 作者有明確專業背景 + 知名平台（arXiv、官方 blog、IEEE、ACM）+ 近 6 個月內 |
| **MEDIUM**       | Medium/Dev.to 上有署名作者 + 內容技術細節正確 + 可交叉驗證                |
| **LOW / 不接受** | 匿名、無日期、無法驗證的二手摘要、行銷文章                                |

PM 若標記 FLAGGED，員工需在隔天補交一份替代來源。

---

## 個人技能累積檔案（HR 維護）

`e1-market-analyst.md` 等個人檔案由 **HR 負責更新**，不由員工自行填寫。
這些檔案是員工技能成長的長期記錄，只有 PM-VERIFIED 的條目才會被納入。
