# /security-check — 執行 ADR-S004 安全基線核查
# 用法：/security-check [target]
# 範例：/security-check E8   → 只核查 Backend
#       /security-check all  → 核查 E7+E8+E13
#       /security-check      → 預設核查所有工程輸出
# 執行者：PM

呼叫 `security_checker` skill，依 $ARGUMENTS 指定對象執行 security-baseline.md 的 9 項安全核查。

執行流程：
1. 解析 $ARGUMENTS 確認核查對象（E7/E8/E13/all）
2. 對每個對象逐項執行 SEC-01 至 SEC-09
3. 無 AI 功能時 SEC-08/09 標記 NOT_APPLICABLE
4. 任何 CRITICAL FAIL → OVERALL = FAIL，停止其他核查並立即回報
5. 輸出 Security Check Report

輸出格式：
```
===
SECURITY_CHECK_REPORT
Date      : [YYYY-MM-DD]
Target    : [E7 / E8 / E13 / ALL]
OVERALL   : PASS | FAIL

  SEC-01 Hardcoded Secrets   : ✅ PASS | ❌ FAIL
  SEC-02 Input Validation    : ✅ PASS | ❌ FAIL
  SEC-03 SQL Injection       : ✅ PASS | ❌ FAIL
  SEC-04 Rate Limiting       : ✅ PASS | ❌ FAIL
  SEC-05 CORS Config         : ✅ PASS | ❌ FAIL
  SEC-06 Password Hashing    : ✅ PASS | ❌ FAIL
  SEC-07 HTTP-only Cookies   : ✅ PASS | ❌ FAIL
  SEC-08 AI Guardrails       : ✅ PASS | ❌ FAIL | — N/A
  SEC-09 LLM Key Mgmt        : ✅ PASS | ❌ FAIL | — N/A

DECISION: APPROVED | REVISION_REQUIRED
===
```

遵守規則：
- ADR-S004：安全違規 = 自動 FAIL，強制 revision
- 任何 FAIL 必須 assign 給對應 engineer 修正後重跑 /security-check
