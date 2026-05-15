---
directive_id: CEO_DIRECTIVE_001
issued_by: CEO
issued_to: PM
date: 2026-04-05
subject: 新增 E6 UI/UX Designer — 公司架構變更
status: ACTIVE
---

# CEO_DIRECTIVE_001
# 新增 UI/UX Designer Agent（E6）— 架構重組

---

## 決策摘要

於現有 E5 Product Designer 與 E6 Frontend Engineer 之間插入新 agent：
**E6 UI/UX Designer（Code: UX）**

現有 E6 → E7（Frontend Engineer）
現有 E7 → E8（Backend Engineer）
現有 E8 → E9（QA Engineer）

總 agent 數量：11 → 12

---

## 決策依據

E5 Product Designer 的 DesignSpec 描述「元件行為與結構」，但不定義「視覺決策與互動細節」。
E6 Frontend Engineer 在實作時被迫填補視覺決策空白，實質違反 RULE-FE-01（禁止設計決策）。
新增 UI/UX Designer 填補這段 domain gap，使前端得到明確的視覺依據，不需自行判斷。

---

## PM 必須執行的更新清單

以下文件必須在此 DIRECTIVE 發出後完成同步：

```
[ ] .claude/agents/e6-ui-ux-designer.md          ← HR 新增
[ ] .claude/agents/e7-frontend-engineer.md        ← 重命名（原 e6），更新 agent_id
[ ] .claude/agents/e8-backend-engineer.md         ← 重命名（原 e7），更新 agent_id
[ ] .claude/agents/e9-qa-engineer.md              ← 重命名（原 e8），更新 agent_id
[ ] .claude/agents/e6-frontend-engineer.md        ← 刪除（已重命名為 e7）
[ ] .claude/agents/e7-backend-engineer.md         ← 刪除（已重命名為 e8）
[ ] .claude/agents/e8-qa-engineer.md              ← 刪除（已重命名為 e9）
[ ] .claude/rules/domain-lock.md                  ← 加入 E6 domain lock；E6→E7/E7→E8/E8→E9 重編
[ ] .claude/rules/phase-gates.md                  ← Phase 3 gate 加入 UISpec AC
[ ] CLAUDE.md                                     ← Agent 名單、Phase 流程圖、目錄結構更新
```

---

## 新 E6 Domain 定義（HR 執行依據）

```
E6 UI/UX Designer
  Input  : DesignSpec（E5）+ MVPDefinition（E4）
  Output : UISpec JSON
  Can    : 視覺設計決策、互動狀態定義、動態規格、畫面視覺組成
  Cannot : Code、產品 scope 決策、市場/競品研究、test plans、新增 DesignSpec 以外的功能
```

## E7 Frontend Engineer 行為變更

```
Input 主要設計依據：DesignSpec（E5）→ UISpec（E6）
新增限制：不得自行做任何視覺設計決策（完全依賴 UISpec）
保留能力：可自行決定 component 拆分方式與 design system 工程架構（屬於工程決策，非視覺決策）
```

---

## 授權聲明

此決策已由人工操作者（CEO）批准。
HR 依此 DIRECTIVE 執行 agent 新增，無需額外 CEO 批准。
PM 負責驗證 HR 輸出並完成所有文件同步。

```
Signed: CEO
Date  : 2026-04-05
```
