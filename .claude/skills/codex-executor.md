---
name: codex-executor
description: |
  PM 專用工具 — 委派小型編碼微任務給 OpenAI Codex CLI 執行。
  適用於不需要完整 E7/E8 phase 流程的原子級程式碼任務：
  utility function、型別定義、設定檔生成、schema 建立、資料轉換腳本、程式碼片段。
  PM 呼叫此 skill 時，Codex 作為輕量級執行 agent，PM 仍需對輸出進行 Validation。
  當任務涉及單一檔案、單一職責、預估 ≤ 50 行時，優先使用此 skill 而非開立完整 E7 Task Spec。
---

# Codex Executor Skill
# PM 委派小型編碼微任務的執行工具

---

## 這個 Skill 是什麼

Codex CLI 是 OpenAI 的本地編碼 agent，能在專案目錄下非互動式執行程式碼任務。
PM 使用此 skill 來處理不值得開立完整 Task Spec 的微小編碼工作，
例如「幫我生成這個介面的 TypeScript 型別」或「寫一個把 CSV 轉 JSON 的 Node.js 腳本」。

**Domain 澄清：PM 使用此 skill 委派任務，輸出由 Codex 產生，不視為 PM 自行產出 code。**
PM 仍必須對輸出進行 binary PASS/FAIL Validation。

---

## 前置需求

### 環境設定
```bash
# 加入 PATH（每個 session 執行一次，或加入 .bashrc）
export PATH="/sessions/funny-loving-faraday/mnt/my-project/.tools/bin:$PATH"

# 設定 OpenAI API Key（必要）
export OPENAI_API_KEY="your-api-key-here"
```

### 確認 CLI 可用
```bash
codex --version
# 預期輸出：codex-cli 0.118.x
```

---

## 適用的微任務類型

| 適合委派給 Codex | 不適合（應開立正式 Task Spec 給 E7/E8） |
|---|---|
| 單一 utility function（≤ 50 行） | 完整 feature 實作 |
| TypeScript 型別 / interface 定義 | 需要 API 整合的功能 |
| 設定檔（tsconfig、eslint、vite.config） | 跨多個檔案的重構 |
| JSON schema / Zod schema 生成 | 有安全要求的 endpoint |
| 資料轉換腳本（CSV↔JSON、格式化） | Phase 4 正式交付物 |
| 測試資料 fixture 生成 | 需要 DB schema 或 migration |
| 簡單的 CLI 工具腳本 | 任何需要正式 Task Spec 驗收的技術決策 |

---

## 呼叫方式

### 基本語法
```bash
codex exec "<任務描述>"
```

### 推薦做法：在專案目錄下執行，並提供清楚的 context
```bash
# 切換到專案目錄
cd /path/to/project

# 執行微任務
codex exec "建立一個 TypeScript interface 定義 User 物件，
包含 id (string)、email (string)、createdAt (Date)、
role ('admin' | 'user' | 'guest') 欄位。
輸出單一 .ts 檔案，命名為 types/user.ts"
```

### 使用 stdin 傳入較長的 context
```bash
cat existing-schema.json | codex exec "根據這個 JSON schema，
生成對應的 TypeScript 型別定義，輸出到 types/generated.ts"
```

### 搭配工作目錄
```bash
# 進到特定專案子目錄再執行
cd /sessions/funny-loving-faraday/mnt/my-project/src
codex exec "在 utils/ 目錄下建立 formatDate.ts，
實作 formatDate(date: Date, format: string): string function，
支援 YYYY-MM-DD 和 DD/MM/YYYY 兩種格式"
```

---

## 撰寫有效 Prompt 的技巧

好的 Codex prompt 具備四個元素：

1. **明確的輸出檔案名稱與路徑**
   - ✓ `輸出到 src/utils/slugify.ts`
   - ✗ `幫我寫個轉 slug 的函數`

2. **具體的函數簽名或介面規格**
   - ✓ `function slugify(text: string, options?: { lowercase?: boolean }): string`
   - ✗ `要能處理各種輸入`

3. **邊界條件說明**
   - ✓ `空字串回傳空字串；非 ASCII 字元轉為連字號`
   - ✗ `要處理特殊字元`

4. **技術棧限制**
   - ✓ `使用純 TypeScript，不引入第三方套件`
   - ✗ `隨便寫就好`

### Prompt 模板
```
在 [路徑] 建立 [檔案名稱]。

實作 [函數/介面/設定] 的定義：
- [規格 1]
- [規格 2]
- [邊界條件]

技術要求：
- 語言/框架：[TypeScript/Python/Node.js 等]
- 不使用第三方套件（或指定允許的套件）
- 包含 JSDoc / docstring 說明
```

---

## 輸出驗證（PM 責任）

Codex 執行後，PM 必須對輸出進行 Validation。驗證檢查清單：

```
[ ] 輸出檔案已在正確路徑建立
[ ] 程式碼無語法錯誤（可用 tsc --noEmit 或 python -m py_compile 驗證）
[ ] 函數簽名符合 Task Spec 要求
[ ] 沒有 hardcoded secrets（security-baseline.md SEC-01）
[ ] 沒有引入未授權的第三方套件
[ ] 實作符合 Task Spec 的所有 AC
```

若有任何 AC FAIL，PM 需要 revision request 並重新呼叫 Codex（或升級為正式 E7 Task）。

---

## 錯誤處理

| 錯誤情況 | 處理方式 |
|---|---|
| `OPENAI_API_KEY` 未設定 | 設定環境變數後重試 |
| Codex 輸出超出 Task 範圍（domain 違規） | 打回，重新撰寫更精確的 prompt |
| 任務複雜度超出微任務定義 | 升級為正式 E7/E8 Task Spec |
| 輸出品質不符 AC | 重新呼叫並在 prompt 中附上失敗原因 |
| Codex 修改了不在 scope 內的檔案 | 用 git diff 確認影響範圍，必要時 git checkout 還原 |

---

## 典型使用場景

### 場景 A：PM 需要一個 helper function
```bash
cd /sessions/funny-loving-faraday/mnt/my-project
codex exec "在 src/utils/validators.ts 新增一個 isValidEmail(email: string): boolean 函數，
使用 RFC 5322 regex 驗證，不引入第三方套件，包含 JSDoc"
```

### 場景 B：生成設定檔
```bash
codex exec "在專案根目錄建立 .eslintrc.json，
配置適合 TypeScript + React 專案的 ESLint 規則，
使用 @typescript-eslint/recommended 和 react-hooks 插件"
```

### 場景 C：從現有程式碼生成型別
```bash
cat src/api/response.json | codex exec "根據這個 JSON 範例，
生成完整的 TypeScript interface，儲存至 src/types/api.ts，
所有屬性加上可選標記（?）除非明確必要"
```

---

## 與 E7 Frontend / E8 Backend 的分工

```
微任務（≤ 50 行，單一職責）        → Codex Executor
  ↓ 不符合以下任一條件時升級

需要以下任一項時 → 開立正式 E7/E8 Task Spec：
  - 實作 MVP must_have feature
  - 需要 API 設計決策
  - 涉及 DB schema 或 migration
  - 跨多個模組的整合
  - 需要正式 Task Spec 驗收的技術決策
  - 估計 > 50 行或影響 > 2 個檔案
```
