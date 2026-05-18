===
CEO_DIRECTIVE
Directive ID : CEO_DIRECTIVE-HR-001
Date         : 2026-04-05
From         : CEO
To           : PM → HR
===

## 指令內容

批准新增以下兩個 agent，授權 HR 立即設計 agent profile：

---

### E11 — DevOps/SRE Engineer（合併角色）

**定位**：cross-phase，公司層級基礎設施監控與部署工程師

**職責範圍**：
- 所有公司產品的 CI/CD pipeline 設計與維護
- 部署流程（staging → production）
- Infrastructure monitoring（uptime、error rate、resource usage）
- 監控範圍：所有已 SHIP 的產品，不限於 agent visibility system
- 與 E9 Operations Monitor 的邊界：E9 負責 agent session 層級監控（agent 狀態、phase gate 偵測），E11 負責產品基礎設施層級監控（server、deployment、infra health）

**Phase 歸屬**：cross-phase（Phase 4 之後介入，與 E9 並列）

---

### E12 — System Architect

**定位**：跨產品技術架構決策者

**職責範圍**：
- 新產品啟動前的架構評估
- 跨產品技術一致性審查（防止技術棧碎片化）
- Phase 1 並行期間的技術可行性裁定（協同 E3 Domain Consultant）
- 技術債評估與泛化規劃（如 v1 → v1.5 → v2 架構路線圖）
- Domain Lock 邊界：E12 提案架構，E7/E8 執行實作，E12 不寫 code

**Phase 歸屬**：Phase 1（並行）+ Phase 4（架構審查）

---

## HR 執行指示

1. 為 E11、E12 各產出一份 agent profile（.claude/agents/*.md，含 YAML frontmatter）
2. 更新 .claude/rules/domain-lock.md，加入 E11 和 E12 的 domain 邊界
3. 完成後通知 PM 進行 Validation

## 同步更新範圍

PM 在 HR 完成後負責更新：
- .claude/rules/domain-lock.md（E11、E12 硬規則）
- 公司 CLAUDE.md（agent 名單、Phase 流程圖）

===
END CEO_DIRECTIVE-HR-001
CEO | 2026-04-05
===
