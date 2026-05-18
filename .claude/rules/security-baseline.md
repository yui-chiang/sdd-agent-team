# Security Baseline — 安全基線強制規範
# 適用範圍：所有工程輸出（E7、E8、E13）
# 依據：系統初始化即生效，不得被 agent 自行 supersede

---

## 強制安全基線（任一項缺失 = 自動 FAIL）

以下 7 項為強制安全基線，PM 在 Phase 4 gate 前必須逐項核查：

```
[ ] SEC-01：無 hardcoded secrets / credentials / tokens（含 LLM API keys）
            → grep -r "sk-\|api_key\s*=\|password\s*=\|token\s*=" --include="*.py,*.ts,*.js,*.env*"
[ ] SEC-02：所有 API endpoints 有 input validation（malformed request 回 400）
            → 確認每個 endpoint handler 有 schema validation 或 pydantic/zod 等
[ ] SEC-03：SQL 使用 parameterized queries 或 ORM，禁止字串拼接
            → grep -r "f\"SELECT\|f'SELECT\|\"SELECT.*+\|'SELECT.*+" --include="*.py,*.ts"
[ ] SEC-04：Auth endpoints 有 rate limiting
            → 確認 /login、/register、/token 等路由有 rate limit middleware
[ ] SEC-05：CORS 限制為已知 origins（禁止 allow_origins=["*"]）
            → grep -r 'allow_origins.*\*\|CORS.*\*' --include="*.py,*.ts,*.js"
[ ] SEC-06：密碼使用 bcrypt（min cost 12）
            → 確認 password hash function 有 bcrypt + rounds >= 12
[ ] SEC-07：Token 使用 HTTP-only cookies（瀏覽器 client）
            → 確認 Set-Cookie header 包含 HttpOnly 和 Secure 屬性
```

**AI 功能額外安全項（E13 輸出）：**
```
[ ] SEC-08：AI input guardrails 已實作（防 prompt injection）
            → 確認 user input 傳入 LLM 前有 sanitization 或 content policy check
[ ] SEC-09：LLM API keys 透過環境變數注入，不出現在程式碼或設定檔中
```

---

## 嚴重性定義

| 層級 | 定義 | PM 處置 |
|------|------|---------|
| CRITICAL | SEC-01 至 SEC-09 任何一項 | 強制 FAIL，不得 APPROVED，打回 E7/E8/E13 修正 |
| HIGH | 無 input validation（非 SQL）| FAIL，需 revision |
| MEDIUM | 錯誤處理不完整 | 視情況 FAIL 或在 known_limitations 標記 |

**硬規則：CRITICAL 未解 = 強制 DO_NOT_SHIP（Phase 5 gate 規定）。**

---

## 引用此規範的場合

- Phase 4 gate checklist（`phase-gates.md` PHASE 4 GATE）
- `/security-check` command（呼叫 `security_checker` skill）
- E7、E8、E13 的 Task Specification 安全驗收條件
- Phase 5 ship 決策前的最終安全複核

---

## 更新規則

若需新增安全條目：
1. PM 提出，CEO 批准（重大安全規範變更需 CEO_DIRECTIVE 文件留底）
2. 或 PM 評估為 HIGH 以下影響，可自行新增並在本文件留底
