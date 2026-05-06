# /generate-prd-pipeline

從一份需求來源（會議記錄、Slack 討論、口頭描述）出發，執行完整的 PRD 生成流水線，最終產出一份通過驗證的正式 PRD 草稿。

**此 skill 串聯以下四個步驟：**
> extract-requirements → draft-prd → gen-test-cases → validate-prd（→ review-prd 選用）

---

## 使用方式

```
/generate-prd-pipeline
```

執行後，pipeline 會以對話方式引導你完成每個階段。你可以在任何階段暫停、修改輸出後再繼續。

---

## Pipeline 流程

### 階段 0：收集輸入

在開始前，請提供以下資訊（逐項詢問，若已知可一次提供）：

1. **需求來源**：貼上或描述原始需求內容（會議記錄 / Slack / 口頭描述 / PM 筆記）
2. **目標系統**：Register / Billing / Declare / Referral / Pharmacy / OPD / ER / IPD / ERNIS / IPDNIS / Portal（擇一）
3. **功能名稱**：這個 PRD 要描述的功能名稱（用於 `title` 和檔名）
4. **負責 PM**：PRD 的 `author` 姓名
5. **生成方式**：標記此 PRD 為 `ai-assisted`（人工提供需求，AI 協助撰寫）或 `ai-generated`（完全由 AI 生成草稿）

---

### 階段 1：需求萃取（extract-requirements）

**目標：** 從原始需求來源萃取結構化需求，映射至 PRD 六節架構。

執行以下萃取作業：

1. **角色識別**：從輸入內容識別所有涉及的使用者角色，對應至 xHIS 14 個標準角色
2. **需求分類**：將需求條目分類為功能性需求（Functional）或非功能性需求（Non-functional）
3. **範疇劃定**：明確識別 In Scope / Out of Scope，特別注意與其他子系統的邊界
4. **業務規則提取**：識別所有涉及的業務規則，對照 CLAUDE.md 第 10 節的 X-BR 規則
5. **不明確項目標記**：無法從輸入確認的細節，標記 `（待確認）`

萃取結果以結構化清單呈現，供下一階段使用。確認清單無誤後進入階段 2。

---

### 階段 2：PRD 草稿生成（draft-prd）

**目標：** 依據階段 1 萃取結果，生成完整的 PRD 六節架構。

生成規則：

**Frontmatter**
```yaml
---
title: "[功能名稱]"
system: "[代碼] [中文名稱]"
version: "1.0"
author: "[負責 PM]"
created: "[今日日期 YYYY-MM-DD]"
updated: "[今日日期 YYYY-MM-DD]"
status: "編輯中"
is_example: false
generated_by: "[ai-assisted 或 ai-generated]"
---
```

**Section 1 — Change History**
建立初版記錄：版本 1.0、今日日期、作者、說明「初版建立（AI 輔助生成）」

**Section 2 — Requirement Overview**
- 2.1 背景與目的：說明現行痛點與本 PRD 解決的問題（不少於 80 字）
- 2.2 目標與範疇：Goals 以 `- [ ]` checkbox 格式列出可量化目標；In/Out Scope 各至少 2 項
- 2.3 目標使用者：表格含角色、描述、主要操作情境
- 2.4 非功能需求：表格含效能、安全性、相容性、可用性（四項均為必填）

**Section 3 — Business Flow Overview**
- 3.1 使用 `flowchart TD` 格式的 Mermaid 圖，涵蓋正常流程與主要分支
- 3.2 步驟說明表：步驟 / 執行角色 / 動作描述 / 備註
- 3.3 與其他系統的互動表：觸發方向（←/→）/ 來源 / 目標 / 說明

**Section 4 — Data Flow Overview**
- 4.1 使用 `flowchart LR` 格式的 Mermaid 圖，包含前端 / 後端 / 其他系統三個 subgraph
- 4.2 關鍵資料項目表：資料名稱 / 說明 / 來源 / 格式或長度 / 必填
- 4.3 API 規格表：端點 / 方法 / 說明 / 主要參數（格式：`/api/v1/{system}/{resource}`）

**Section 5 — Use Cases**
- 每個 UC 獨立一節，格式為：
  - UC 標題（格式：`### UC-0N｜[角色] [動作]`）
  - 角色（Actor）、前置條件、後置條件
  - 5.x.1 Main Flow 表格
  - 5.x.1 Exception Flow 表格
  - 5.x.2 UI 畫面參考（Figma 連結留 `（請填入）`；畫面說明至少 3 個 UI 區域）
  - 5.x.3 欄位與互動規格（Spec）表格
  - 業務規則（BR-xx）列表

**Section 6 — Test Cases**
（此節在階段 3 由 gen-test-cases 填入）

生成草稿後，列出以下摘要供人工確認：
- UC 清單（幾個 UC）
- 待確認項目列表
- 引用的跨系統業務規則

確認草稿無誤後進入階段 3。

---

### 階段 3：測試案例生成（gen-test-cases）

**目標：** 為每個 UC 生成驗收測試案例，填入 Section 6。

生成規則：
- **P0**：正常流程的核心路徑、必填欄位驗證、關鍵業務規則
- **P1**：例外流程、邊界值、系統整合點
- **P2**：選填欄位、UI 細節、效能邊界

每個 TC 格式：

| TC ID | 對應 UC | 測試情境 | 前置條件 | 測試步驟 | 預期結果 | 優先級 |
|-------|--------|---------|--------|---------|--------|------|

每個 UC 至少生成：
- 2 個 P0 TC（正常流程 + 最重要的業務規則驗證）
- 2 個 P1 TC（關鍵例外流程）
- 視需要補充 P2 TC

---

### 階段 4：結構驗證（validate-prd）

**目標：** 確認生成的 PRD 通過所有驗證規則，等同於 CI 通過。

執行驗證項目（同 `/validate-prd` 定義）：
- Frontmatter 所有欄位完整且格式正確
- `generated_by` 欄位存在且值正確
- 六節架構完整
- 節內容品質（Mermaid 圖、表格、最低字數）
- 待確認項目統計

**輸出驗證報告。若有 ❌ 錯誤，在此自動修正後重新驗證，直到全數通過為止。**

---

### 階段 5（選用）：PR 準備

驗證通過後，提供以下輸出供 PM 直接使用：

**建議的 git commit message：**
```
feat([系統代碼]): add PRD-[FeatureName].md — [功能名稱] v1.0 初版

generated_by: [ai-assisted/ai-generated]
待確認項目：[N] 項（見文件中 （待確認） 標記）

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
```

**建議的 PR 說明：**
```markdown
## PRD: [功能名稱]

**系統：** [系統代碼]
**版本：** 1.0
**狀態：** 編輯中

### 本 PR 包含
- [功能名稱] PRD 初版（AI 輔助生成）

### 生成方式
- generated_by: [ai-assisted/ai-generated]
- Pipeline: extract-requirements → draft-prd → gen-test-cases → validate-prd

### 需要審閱者確認
[列出所有 （待確認） 項目]

### 驗證結果
- CI: validate-prd ✅
- 人工驗證建議使用 `/review-prd` 進行 6 維度審查
```

**建議檔案路徑：** `[系統代碼]/PRD-[FeatureName].md`

---

## Pipeline 狀態追蹤

每完成一個階段，輸出進度標示：

```
Pipeline 進度：[階段 0 收集輸入 ✅] → [階段 1 需求萃取 ✅] → [階段 2 草稿生成 ✅] → [階段 3 測試案例 ✅] → [階段 4 驗證 ✅] → [階段 5 PR 準備 ✅]
```

---

## 使用建議

- 此 pipeline 適合從**零開始**生成一份新 PRD。若只需要局部生成（如只補 TC），直接使用對應 skill（`/gen-test-cases`、`/draft-prd` 等）更有效率。
- 每個階段結束後，PM 應審閱並確認輸出，再繼續下一階段。AI 生成的業務規則和技術細節需人工驗證。
- `generated_by: "ai-generated"` 的 PRD 在進入 `內部審核通過` 狀態前，需經過 PM lead 完整審查。
