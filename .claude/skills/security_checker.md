# Skill: security_checker
# Owner: PM | 用途：執行 ADR-S004 安全基線核查，產出 PASS/FAIL 結果
# 觸發：/security-check 或 Phase 4 gate 前

---

## 目的

對 E7（Frontend）、E8（Backend）、E13（AI 工程，若存在）的輸出執行
`security-baseline.md` 定義的 9 項安全基線核查，產出 Security Check Report。

---

## 執行步驟

```
Step 1: 確認核查對象（E7 codebase / E8 codebase / E13 codebase）
Step 2: 對每項安全基線（SEC-01 至 SEC-09）執行核查
Step 3: 若產品無 AI 功能，SEC-08/09 標記 NOT_APPLICABLE
Step 4: 任何 CRITICAL 項目 FAIL → 整體 = FAIL，強制 revision
Step 5: 輸出 Security Check Report
```

---

## SEC-01：Hardcoded Secrets 核查

```bash
# 搜尋常見 secret pattern（在 E7/E8 程式碼根目錄執行）
grep -rn \
  -e "sk-[A-Za-z0-9]" \
  -e "api_key\s*=\s*['\"]" \
  -e "password\s*=\s*['\"]" \
  -e "secret\s*=\s*['\"]" \
  -e "token\s*=\s*['\"]" \
  --include="*.py" --include="*.ts" --include="*.js" \
  --exclude-dir=".git" --exclude-dir="node_modules" --exclude-dir="venv" \
  .

# 若有任何符合結果（非空） → SEC-01 = FAIL
# 若結果為空 → SEC-01 = PASS
```

---

## SEC-02：Input Validation 核查

```
手動確認每個 API endpoint handler：
- Python/FastAPI：有 Pydantic model 作為 request body 參數？
- TypeScript/Express：有 zod/joi/yup schema validation？
- 若直接使用 request.body 或 request.json() 而無 schema → FAIL

重點路由：
- POST /login, /register
- 任何接受 user_id, agent_id, task_id 的路由
- 任何 file upload 路由
```

---

## SEC-03：SQL Injection 核查

```bash
# 搜尋字串拼接 SQL（Python f-string 或字串加法）
grep -rn \
  -e 'f"SELECT\|f'"'"'SELECT' \
  -e '"SELECT.*" +\|'"'"'SELECT.*'"'"' +' \
  -e 'execute(.*%.*)\|execute(.*format(' \
  --include="*.py" --include="*.ts" --include="*.js" \
  --exclude-dir=".git" --exclude-dir="node_modules" \
  .

# 若有使用 ORM（SQLAlchemy, Prisma, Drizzle）可視為通過
# 若有 raw SQL 但用 parameterized（? 或 $1）也可通過
```

---

## SEC-04：Rate Limiting 核查

```
確認以下路由有 rate limit middleware：
- /login
- /register
- /token（token refresh 等）
- /forgot-password
- 任何 SMS/Email 驗證觸發路由

Python：slowapi / fastapi-limiter / redis-rate-limit
Node.js：express-rate-limit / rate-limiter-flexible

若以上路由無任何 rate limit middleware → SEC-04 = FAIL
```

---

## SEC-05：CORS 核查

```bash
grep -rn \
  -e 'allow_origins.*\*' \
  -e 'origins.*\[.*\*.*\]' \
  -e 'Access-Control-Allow-Origin.*\*' \
  --include="*.py" --include="*.ts" --include="*.js" \
  --exclude-dir=".git" --exclude-dir="node_modules" \
  .

# 若 allow_origins=["*"] 且無其他限制 → SEC-05 = FAIL
# 若僅在 development 環境使用 * 且有環境判斷 → 標記警告，不直接 FAIL
```

---

## SEC-06：Password Hashing 核查

```bash
# 確認 bcrypt 使用且 cost >= 12
grep -rn "bcrypt\|passlib\|argon2" --include="*.py" --include="*.ts" .

# 找到後確認：
# Python bcrypt：bcrypt.hashpw(password, bcrypt.gensalt(rounds=12))
# passlib：CryptContext(schemes=["bcrypt"], deprecated="auto")，bcrypt_rounds >= 12
# 若找不到任何 bcrypt/argon2 使用 → SEC-06 = FAIL（若有密碼功能的情況下）
```

---

## SEC-07：HTTP-only Cookies 核查

```bash
grep -rn "Set-Cookie\|set_cookie\|response.cookie" \
  --include="*.py" --include="*.ts" --include="*.js" \
  --exclude-dir=".git" --exclude-dir="node_modules" \
  .

# 確認所有 token 相關 cookie 有 HttpOnly=True 和 Secure=True
# 若 token 存在 localStorage 而非 HttpOnly cookie → 標記警告（前端 XSS 風險）
```

---

## SEC-08/09：AI 安全核查（E13 輸出，非 AI 產品可 NOT_APPLICABLE）

```
SEC-08：Input Guardrails
  - 確認 user message 傳入 LLM 前有 content filter 或 prompt injection 防護
  - 確認 system prompt 與 user input 有明確分隔
  - 確認不會直接將 user input 拼接進 system prompt

SEC-09：LLM API Keys
  - grep -rn "OPENAI_API_KEY\|ANTHROPIC_API_KEY\|sk-" .claude/ app/ --include="*.py,*.ts"
  - 確認 key 只透過環境變數取得（os.environ / process.env），不 hardcode
```

---

## Security Check Report 格式

```
===
SECURITY_CHECK_REPORT
Date      : [YYYY-MM-DD]
Checked by: PM
Target    : [E7 / E8 / E13 / 全部]
OVERALL   : PASS | FAIL

RESULTS:
  SEC-01 Hardcoded Secrets    : PASS | FAIL | NOT_APPLICABLE
  SEC-02 Input Validation     : PASS | FAIL | NOT_APPLICABLE
  SEC-03 SQL Injection        : PASS | FAIL | NOT_APPLICABLE
  SEC-04 Rate Limiting        : PASS | FAIL | NOT_APPLICABLE
  SEC-05 CORS Config          : PASS | FAIL | NOT_APPLICABLE
  SEC-06 Password Hashing     : PASS | FAIL | NOT_APPLICABLE
  SEC-07 HTTP-only Cookies    : PASS | FAIL | NOT_APPLICABLE
  SEC-08 AI Input Guardrails  : PASS | FAIL | NOT_APPLICABLE
  SEC-09 LLM Key Management   : PASS | FAIL | NOT_APPLICABLE

CRITICAL_ITEMS:
  [列出 FAIL 的 SEC 編號及具體問題]

DECISION: APPROVED | REVISION_REQUIRED
ASSIGN_TO: [E7 / E8 / E13 — 依違規項目分配]
===
```

---

## 違規處理

```
任何 CRITICAL FAIL → OVERALL = FAIL → 打回對應 engineer
不得因時間壓力降低安全標準（ADR-S004）
Security FAIL 記錄在 ADR（安全違規記錄）
修正後需重新執行 /security-check，不接受「已知問題留待後續修正」
```
