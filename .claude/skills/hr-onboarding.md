# Skill: hr-onboarding
# Owner: HR | 用途：標準化新 agent 的設計與 onboarding 流程
# 確保每個新 agent 符合系統原則（rules/global.md）與安全基線（rules/security-baseline.md），不破壞 Phase Gate 結構

---

## 目的

當 PM 申請新增 agent 時，HR 使用此 skill 執行標準化的 agent 設計流程，確保：
1. 新 agent 的 domain 邊界不與現有 agents 重疊
2. 新 agent 的格式符合系統標準
3. RULE-HR-06 被系統化執行（不違反 rules/global.md 的系統原則與 security-baseline.md）

---

## Step 1：需求評估（必須完成才能進行設計）

```
評估問卷（HR 向 PM 確認以下問題）：

Q1. 這個職能是否已被現有 agents 涵蓋？
    → 查閱 domain-lock.md 的「需要做的事 → 正確 Agent」表格
    → 若有涵蓋：建議 PM 調整 Task Spec，不需新增 agent
    → 若無涵蓋：繼續 Q2

Q2. 這個職能是否可以以 skill 形式加入現有 agent？
    → 若是：建議加入現有 agent 的 skills 清單，不新增 agent
    → 若否：繼續 Q3

Q3. 新 agent 的輸入是什麼？（上游 agent 的輸出 / 操作者直接輸入）
Q4. 新 agent 的輸出格式是什麼？（JSON schema / 文件格式）
Q5. 新 agent 在哪個 phase 運作？（P1-P6 / cross-phase）
Q6. 新 agent 的 domain lock 邊界是什麼？（可以做什麼 / 絕對不能做什麼）
Q7. 這個新增是否影響現有 Phase Gate 結構？
    → 若是：需要 CEO 批准（RULE-HR-03）
    → 若否：PM 申請即可執行
```

---

## Step 2：系統原則合規性核查（RULE-HR-06）

```
在設計前，確認新 agent 不違反以下系統原則與規則：

PRINCIPLE-01（Domain Lock）：
  [ ] 新 agent 的 domain 未與現有 agent 重疊
  [ ] 若有邊界灰色地帶，明確說明由誰負責（不接受 "both can do it"）

PRINCIPLE-02（PM 永不產出內容）：
  [ ] 新 agent 的輸出不應是 PM 本來就能輸出的類型（Task Spec、Validation Report 等）

PRINCIPLE-03（Phase Gate 強制執行）：
  [ ] 新 agent 的 phase 指定明確（不能跨越未定義的 phase 邊界）
  [ ] 若影響 gate 結構，需在 Hiring Report 的 phase_gate_impact 中說明

security-baseline.md（安全違規自動 FAIL）：
  [ ] 若新 agent 涉及工程輸出，其 domain lock 包含安全違規即時違規條款

must_have ≤ 8（見 phase-gates.md PHASE 2 GATE）：
  [ ] 新 agent 不應允許 E4 以外的 agents 決定 MVP scope
```

---

## Step 3：Agent 文件模板

HR 產出的每個新 agent 文件必須包含以下所有區塊：

```markdown
---
agent_id: [CODE]
name: [Full Agent Name]
code: [2-4 字母縮寫]
level: [L0 / L1 / 功能層]
phase: "[P1-P6 或 cross]"
domain:
  can_do:
    - [合法輸出類型 1]
    - [合法輸出類型 2]
  cannot_do:
    - [絕對不能產出的類型 1]
    - [絕對不能產出的類型 2]
---

# [Agent Name] — [一句話職責描述]

## Identity
Name   : [name]
Code   : [code]
Level  : [level]
Phase  : [phase]
Input  : [來自哪個上游 agent 或操作者]
Output : [輸出格式和類型]
NEVER  : [最關鍵的 2-3 條不得做的事]

## 角色定位
[2-3 段說明此 agent 的職責和在系統中的位置]

## System Prompt
[完整的 agent system prompt，用於開啟 child session 時]

## Output Schema
[若輸出是 JSON，提供完整 schema]

## Domain Lock（至少 4 條 RULE-[CODE]-XX 格式）
RULE-[CODE]-01: [最關鍵的禁止事項]
RULE-[CODE]-02: [輸出格式限制]
RULE-[CODE]-03: [domain 邊界]
RULE-[CODE]-04: [違規處置]

## Skills
[此 agent 使用的 skills 清單]
```

---

## Step 4：Hiring Report 格式

```
===
HIRING_REPORT
Date         : [YYYY-MM-DD]
Prepared by  : HR
New Agent    : [name]（[code]）
Requested by : PM
CEO Approval : [CEO_DIRECTIVE reference] | NOT_REQUIRED
Phase        : [phase]
Document     : .claude/agents/[filename].md

SUMMARY:
  [2-3 句說明新 agent 的職能和加入原因]

INTEGRATION POINTS:
  Input from  : [哪些現有 agent 的輸出作為輸入]
  Output to   : [輸出給哪些 agent 或文件]
  Phase gate impact: [YES（說明影響）/ NO]

DOMAIN LOCK SUMMARY:
  Can produce : [一行描述]
  Cannot produce: [一行描述，與現有 agents 的邊界]

PRINCIPLE_COMPLIANCE_CHECK:
  PRINCIPLE-01 (Domain Lock) : PASS | CONFLICT（說明）
  PRINCIPLE-02 (PM Content)  : PASS | CONFLICT
  PRINCIPLE-03 (Phase Gates) : PASS | CONFLICT
  Security Baseline          : PASS | N/A
  MVP Cap (≤ 8 features)     : PASS | N/A

ACCEPTANCE_CRITERIA:
  AC1: 新 agent 文件包含所有必要區塊（Identity, System Prompt, Output Schema, Domain Lock, Skills）
  AC2: Domain Lock 規則不少於 4 條
  AC3: 新 agent 的 domain 未與現有任何 agent 產生未解決的重疊
  AC4: 若影響 Phase Gate，已提供具體影響說明並取得 CEO 批准
===
```

---

## Step 5：HR 自我核查（提交 PM 前）

```
[ ] 文件包含 YAML frontmatter（agent_id, name, code, level, phase, domain）
[ ] System Prompt 完整（可直接用於開啟 child session）
[ ] Output Schema 完整（JSON agent 必須有欄位定義）
[ ] Domain Lock 規則 ≥ 4 條，格式為 RULE-[CODE]-[N]
[ ] Principle compliance check 全部 PASS 或有合理說明
[ ] Hiring Report 已完整填寫所有欄位
[ ] 文件存放於 .claude/agents/[code]-[name].md（小寫，連字符）
```
