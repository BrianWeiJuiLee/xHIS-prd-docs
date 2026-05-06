# /validate-prd

在 commit 或發 PR 前，對指定 PRD 文件執行完整的本地驗證。驗證通過才代表文件符合 xHIS PRD 規範，可進入 review 流程。

## 使用方式

```
/validate-prd <PRD 檔案路徑>
```

例如：
```
/validate-prd OPD/PRD-OrderEntry.md
/validate-prd Register/PRD-PatientSearch.md
```

若不傳入路徑，則對當前目錄下所有 `PRD-*.md`（不含 `is_example: true`）執行批次驗證。

---

## 驗證項目清單

### A. Frontmatter 完整性（必填欄位）

逐一檢查以下欄位存在且非空白：

| 欄位 | 驗證規則 |
|-----|---------|
| `title` | 非空；`is_example: true` 時需以「【範例】」開頭 |
| `system` | 格式：`{代碼} {中文名}` — 代碼必須是 Register / Billing / Declare / Referral / Pharmacy / OPD / ER / IPD / ERNIS / IPDNIS / Portal 其中之一 |
| `version` | 格式：`X.Y`（例如 `1.0`、`1.2`、`2.0`） |
| `author` | 非空 |
| `created` | 格式：`YYYY-MM-DD` |
| `updated` | 格式：`YYYY-MM-DD`，且 ≥ `created` |
| `status` | 嚴格枚舉：`編輯中` / `內部審核通過` / `外部審核通過` / `實作中` / `已上線` |
| `is_example` | 布林值：`true` 或 `false`（非字串） |
| `generated_by` | 枚舉：`human` / `ai-assisted` / `ai-generated` |

### B. 六節架構完整性

確認 Markdown 中包含以下六個 H2 節（## 編號）：

1. `## 1.` — Change History
2. `## 2.` — Requirement Overview
3. `## 3.` — Business Flow Overview
4. `## 4.` — Data Flow Overview
5. `## 5.` — Use Cases
6. `## 6.` — Test Cases

### C. 節內容品質檢查

- **Section 1**：Change History 表格包含至少一筆版本紀錄，且 `updated` 欄位與最新版本的日期一致
- **Section 2.1**：背景與目的文字不為空且字數 > 50
- **Section 2.2**：含有目標（Goals）、範疇內（In Scope）、範疇外（Out of Scope）三個子段落
- **Section 2.3**：目標使用者表格有至少一筆角色資料
- **Section 2.4**：非功能需求表格包含效能（效能/Performance）與安全性（安全/Security）
- **Section 3.1**：包含 Mermaid code block（\`\`\`mermaid）
- **Section 4.1**：包含 Mermaid code block
- **Section 4.3**：API 規格表格包含至少一筆 API 端點
- **Section 5**：至少一個 UC（Use Case）包含操作流程表（Main Flow）與例外流程（Exception Flow）
- **Section 6**：Test Cases 表格包含至少一筆 TC，且每筆 TC 有 P0/P1/P2 優先級

### D. 跨系統一致性檢查

- 文件中引用的子系統名稱是否使用標準代碼（Register、Billing… 等 11 個）
- 3.3 節「與其他系統的互動」是否存在（若 Section 3 提到跨系統呼叫）

### E. 待確認項目統計

列出文件中所有標記為 `（待確認）` 的條目，作為提醒（不視為錯誤，但輸出警告列表）

---

## 輸出格式

每次驗證完成後，輸出以下格式的報告：

```
=== PRD 驗證報告 ===
檔案：OPD/PRD-OrderEntry.md
時間：2026-05-07

[Frontmatter]
  ✅ title: "門診醫令開立"
  ✅ system: "OPD 門診醫令系統"
  ✅ version: "1.0"
  ✅ author: "Brian Lee"
  ✅ created: "2026-05-07"
  ✅ updated: "2026-05-07"
  ✅ status: "編輯中"
  ✅ is_example: false
  ✅ generated_by: "ai-assisted"

[六節架構]
  ✅ Section 1 — Change History
  ✅ Section 2 — Requirement Overview
  ✅ Section 3 — Business Flow Overview
  ✅ Section 4 — Data Flow Overview
  ✅ Section 5 — Use Cases
  ✅ Section 6 — Test Cases

[節內容品質]
  ✅ 2.1 背景與目的（字數: 120）
  ⚠️  2.4 非功能需求：未發現「安全性」項目 — 請確認是否需要補充
  ✅ 3.1 Mermaid 流程圖
  ✅ 4.3 API 規格（3 個端點）
  ✅ 5.1 UC-01 Main Flow + Exception Flow
  ✅ 6.x Test Cases（5 筆，含 P0: 2, P1: 3）

[待確認項目]
  ⚠️  第 34 行：「CDSS 整合規格（待確認）」
  ⚠️  第 87 行：「藥局系統 API 格式（待確認）」

=== 總結 ===
❌ 錯誤：1 項（見上方 ❌）
⚠️  警告：3 項（見上方 ⚠️）

狀態：驗證未通過 — 請修正錯誤後再 commit
```

---

## 驗證結果處置建議

| 結果 | 說明 | 建議行動 |
|-----|-----|---------|
| 全數 ✅ | 文件符合規範 | 可執行 git commit，建議附上 `/update-prd-status` |
| 有 ❌ | 有必填欄位缺漏或結構錯誤 | 修正後重新執行 `/validate-prd` |
| 只有 ⚠️ | 有品質警告但無阻擋性錯誤 | 評估是否補充；可先 commit 但建議 PR 描述中說明 |

---

## 執行說明

此 skill 由 Claude Code 直接執行（讀取文件並逐項核對），無需外部工具。批次模式會一次輸出所有文件的報告並在最後統計通過率。

驗證邏輯與 CI 的 `.github/workflows/validate-prd.yml` 保持一致，確保本地通過即代表 CI 也會通過。
