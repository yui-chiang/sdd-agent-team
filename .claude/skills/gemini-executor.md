---
name: gemini-executor
description: |
  PM 專用工具 — 委派小型文字分析與內容生成微任務給 Google Gemini CLI 執行。
  適用於不需要完整 E1-E4 phase 流程的原子級文字任務：
  快速摘要、內容草稿、文件分析、測試資料生成、Schema 說明文字、錯誤訊息撰寫、技術文件初稿。
  Gemini 擅長多模態輸入（文字 + 圖片 + 檔案）及長文處理，比 Codex 更適合文字密集型任務。
  當任務涉及「產出文字內容而非 code」且為單一明確輸出時，優先考慮此 skill。
---

# Gemini Executor Skill
# PM 委派小型文字分析與內容生成微任務的執行工具

---

## 這個 Skill 是什麼

Gemini CLI 是 Google 的本地 AI agent，支援非互動式（headless）執行與多模態輸入。
PM 使用此 skill 處理文字密集型微任務：摘要、草稿、分析、說明文字等。

**Domain 澄清：**
- Gemini 可以幫 PM 處理「不構成產品決策」的文字輔助工作
- Gemini **不能**替代 E1（市場研究）、E2（競品分析）、E3（Domain 諮詢）的正式輸出
- Gemini 的輸出不具備 JSON schema 規範，不可直接作為 Phase 1-5 的正式交付物

---

## 前置需求

### 環境設定
```bash
# 加入 PATH（每個 session 執行一次，或加入 .bashrc）
export PATH="/sessions/funny-loving-faraday/mnt/my-project/.tools/bin:$PATH"

# 設定 Gemini API Key（二選一）
export GEMINI_API_KEY="your-api-key-here"
# 或使用 Google OAuth（gemini auth login）
```

### 確認 CLI 可用
```bash
gemini --version
# 預期輸出：0.x.x
```

---

## 適用的微任務類型

| 適合委派給 Gemini | 不適合（應開立正式 Task Spec 給對應 Agent） |
|---|---|
| 摘要一份文件或 log 檔 | E1 MarketReport（市場規模、TAM/SAM/SOM） |
| 草擬 error message 文案 | E2 CompetitorReport（競品分析） |
| 從 JSON 生成說明文字 | E3 DomainReport（法規、可行性） |
| 將研究資料整理成條列式筆記 | E4 MVPDefinition（功能決策）|
| 分析一份 log 找出錯誤模式 | E5/E6 設計決策 |
| 生成測試用的假資料（姓名、地址等） | Phase 任何正式交付物 |
| 將英文文件翻譯成繁體中文 | 需要 ADR 記錄的決策 |
| 從 wireframe 說明生成 alt text | — |
| 比較兩份文件的差異 | — |

---

## 呼叫方式

### 基本非互動模式（最常用）
```bash
gemini -p "你的任務描述" --yolo
```

> `-p` = non-interactive mode（headless）
> `--yolo` = 自動接受所有操作，不需人工確認（適合 CI/非互動環境）
> `--approval-mode yolo` 與 `--yolo` 等效

### 傳入檔案內容作為 context
```bash
# 透過 stdin 傳入檔案
cat error.log | gemini -p "分析這份 log，找出所有 ERROR 層級的訊息，
整理成條列式，每條附上出現次數" --yolo

# 直接在 prompt 中引用路徑（Gemini 有 file 讀取能力）
gemini -p "讀取 ./docs/api-spec.md，
為每個 endpoint 生成一段使用說明，輸出到 ./docs/api-guide.md" --yolo
```

### 指定輸出檔案
```bash
gemini -p "根據以下 JSON schema 生成 10 筆測試資料，
輸出為 JSON array，儲存至 src/__fixtures__/users.json：
$(cat src/types/user.ts)" --yolo
```

### 搭配較長 context 的任務
```bash
gemini -p "$(cat << 'EOF'
任務：將以下技術文件翻譯成繁體中文，保留所有程式碼區塊不翻譯。
輸出儲存至 docs/zh-TW/architecture.md。

原始內容：
$(cat docs/architecture.md)
EOF
)" --yolo
```

---

## 撰寫有效 Prompt 的技巧

Gemini 在以下情況表現最好：

1. **明確的輸入來源**
   - ✓ `讀取 ./data/report.csv，計算每個類別的平均值`
   - ✗ `分析資料`

2. **明確的輸出格式**
   - ✓ `輸出為 Markdown 格式，使用 ## 作為章節標題`
   - ✗ `整理一下`

3. **明確的儲存目標**
   - ✓ `結果儲存至 output/summary.md`
   - ✗ `給我看一下`

4. **限制 scope，避免 AI 做產品決策**
   - ✓ `整理這份 interview transcript，列出使用者提到的痛點（原文摘錄，不加詮釋）`
   - ✗ `分析這份訪談，告訴我應該做什麼功能`

### Prompt 模板
```
任務：[一句話說明目的]

輸入：[檔案路徑 或 直接附上內容]

輸出要求：
- 格式：[Markdown / JSON / 純文字]
- 儲存至：[路徑，若需要]
- 長度限制：[可選]

限制：
- [不要做某事，例如：不要新增未在輸入中出現的資訊]
- [語言：繁體中文]
```

---

## 多模態輸入（進階）

Gemini 支援圖片輸入，適合：

```bash
# 分析 UI 截圖（例如從 E6 wireframe 生成 alt text）
gemini -p "描述這張 UI 截圖中的所有互動元素，
為每個元素生成 WCAG 合規的 aria-label 文字，
輸出為 JSON 格式：{componentId: string, ariaLabel: string}[]" --yolo < screenshot.png
```

---

## 輸出驗證（PM 責任）

Gemini 執行後，PM 必須確認：

```
[ ] 輸出檔案在正確路徑
[ ] 內容符合 Task 的 AC
[ ] 無幻覺（hallucination）— 特別是數字、日期、引用
[ ] 若輸出為 JSON，確認可通過 JSON.parse（必要時用 jq . 驗證）
[ ] 輸出不包含產品決策內容（Gemini 輸出建議 ≠ ADR 決策）
[ ] 繁體中文要求已滿足（不含簡體或大陸用語）
```

---

## 錯誤處理

| 錯誤情況 | 處理方式 |
|---|---|
| `GEMINI_API_KEY` 未設定 | 設定 API key 後重試，或執行 `gemini auth login` |
| 輸出混入產品決策 | 打回，重新加入「只整理資訊，不做判斷」的 prompt 限制 |
| 輸出語言為簡體中文 | 在 prompt 末尾加入「使用繁體中文，不使用簡體中文和中國大陸用語」 |
| 任務超出文字範疇（需要執行 code） | 改用 Codex Executor |
| 輸出需要成為正式 Phase 交付物 | 升級為完整 Task Spec 並 assign 給正確 agent |

---

## 與其他 Agents 的分工

```
微型文字任務（≤ 500 字輸出，單一明確目的） → Gemini Executor
  ↓ 需要升級時

E1 Market Analyst     → 市場調查、TAM/SAM、persona 建立
E2 Competitor Analyst → 競品分析、feature matrix
E3 Domain Consultant  → 法規、可行性、domain expertise
E4 Product Strategist → 功能優先順序、user stories、AC
E7 Frontend Engineer  → 需要執行 code 的任務 → 改用 Codex
```

---

## 典型使用場景

### 場景 A：快速摘要一份長文件
```bash
cat docs/user-research-transcripts.md | gemini -p "
摘要這份使用者訪談逐字稿，列出：
1. 使用者明確提到的痛點（原文引用）
2. 使用者描述的工作流程
3. 使用者提出的功能需求（原文引用）
不做任何詮釋或優先順序判斷，純粹整理原文。
輸出為 Markdown，儲存至 docs/interview-summary.md
使用繁體中文" --yolo
```

### 場景 B：生成測試資料
```bash
gemini -p "生成 20 筆符合以下 schema 的測試資料：
$(cat src/types/product.ts)
資料需要多樣化（不同品類、價格區間、庫存狀態）。
輸出為 JSON array，儲存至 src/__fixtures__/products.json" --yolo
```

### 場景 C：翻譯技術文件
```bash
gemini -p "將以下文件翻譯為繁體中文，
保留所有 code block 原文不翻譯，
保留所有 URL、API endpoint 原文不翻譯，
專有名詞第一次出現時附上英文原文（括號）。
儲存至 docs/zh-TW/setup.md：
$(cat docs/setup.md)" --yolo
```

### 場景 D：分析 log 找錯誤
```bash
cat logs/app.log | gemini -p "
分析這份 application log：
1. 列出所有 ERROR 層級的訊息（含時間戳）
2. 找出重複出現的錯誤模式
3. 標注可能影響使用者的錯誤
輸出為 Markdown 表格，不要推測原因，只整理資訊。
儲存至 reports/error-analysis.md" --yolo
```
