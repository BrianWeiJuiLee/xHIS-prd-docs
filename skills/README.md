# xHIS PRD Skills｜AI 輔助工具集

本目錄管理 xHIS PM 團隊共享的 AI Skill，協助 PM 更快、更一致地完成 PRD 相關作業。

## 什麼是 Skill？

Skill 是搭配 **Claude Code** 使用的 AI 指令集，儲存於本 repo 的 `.claude/commands/` 資料夾。
PM clone 本 repo 後，在 repo 目錄下開啟 Claude Code，即可用 `/<skill名稱>` 呼叫，不需要手動安裝任何額外工具。

```
# 範例：呼叫 draft-prd skill
/draft-prd

# Claude Code 會依照 skill 的指令與你對話，逐步完成任務
```

---

## 可用 Skills 總覽

| Skill 指令 | 用途 | 適用情境 |
|-----------|-----|--------|
| [`/draft-prd`](#draft-prd) | 起草新的 PRD 文件 | 功能有了基本構想，需要快速產出 PRD 草稿 |
| [`/review-prd`](#review-prd) | 審查 PRD 品質 | 準備提交 PR 前的自我檢查，或互相 Review |
| [`/gen-test-cases`](#gen-test-cases) | 從 Use Cases 產生 Test Cases | Use Cases 寫完後，自動補出 Test Cases |
| [`/extract-requirements`](#extract-requirements) | 從會議紀錄萃取需求 | 訪談或會議後，快速整理結構化需求 |
| [`/update-prd-status`](#update-prd-status) | 更新 PRD 狀態與版本 | 狀態轉換時（審核通過、實作中等） |
| [`/gen-flow-diagram`](#gen-flow-diagram) | 產生 Mermaid 流程圖 | 需要業務流程圖或資料流程圖時 |
| [`/translate-prd`](#translate-prd) | PRD 中英互譯 | 需要提供英文版本給外籍工程師或合作夥伴 |

---

## 環境設定

### 前置需求

1. 安裝 [Claude Code](https://claude.ai/code)（CLI 版本或 VS Code 擴充功能）
2. 確認已登入 Anthropic 帳號（`claude auth`）
3. Clone 本 repo：
   ```bash
   git clone https://github.com/BrianWeiJuiLee/xHIS-prd-docs.git
   cd xHIS-prd-docs
   ```
4. 在 repo 根目錄啟動 Claude Code：
   ```bash
   claude
   ```

### 確認 Skills 已載入

啟動後，在 Claude Code 介面輸入 `/`，若能看到以下指令清單，表示設定正確：
```
/draft-prd
/review-prd
/gen-test-cases
/extract-requirements
/update-prd-status
/gen-flow-diagram
/translate-prd
```

---

## 各 Skill 詳細說明

### draft-prd

**用途**：從功能描述或需求構想，起草符合 xHIS 六節標準結構的 PRD 草稿

**使用方式**：
```
/draft-prd
```
啟動後，Claude 會詢問功能基本資訊（系統、功能名稱、背景痛點、使用者等），收集足夠資訊後自動產出完整 PRD 草稿。

**也可以這樣用**（帶入背景資料）：
```
/draft-prd 我需要幫 OPD 系統寫一份「Order Set 管理」的 PRD，醫師目前要重複輸入常用醫令，我們想讓他們可以儲存和套用個人常用組合
```

**輸出**：可直接存為 `.md` 檔案的完整 PRD Markdown

---

### review-prd

**用途**：六維度 PRD 品質審查（完整性、清晰度、可測試性、技術可行性、醫療法規、UX）

**使用方式**：
```
/review-prd
```
啟動後，貼上 PRD 全文（或告知檔案路徑），Claude 會逐節審查並輸出結構化審查報告，標明 Blocker 和 Non-blocker 問題。

**建議工作流程**：
1. 寫完 PRD 草稿後，先自己用 `/review-prd` 做一次自我審查
2. 修正 Blocker 問題後再開 PR 請同事 Review
3. PR Reviewer 也可用此 skill 輔助審查

---

### gen-test-cases

**用途**：從 PRD Use Cases 段落自動產生覆蓋率完整的 Test Cases

**使用方式**：
```
/gen-test-cases
```
啟動後，貼上 PRD 的 Use Cases 段落（第 5 節），Claude 會分析操作流程、例外情境、業務規則，自動產出 Test Cases 表格（含 P0/P1/P2 優先級）。

**注意**：生成的 Test Cases 需要 PM 人工確認業務邏輯正確性，不應直接不審查就放入 PRD。

---

### extract-requirements

**用途**：從非結構化文字（會議紀錄、訪談稿、Email）萃取並整理結構化需求

**使用方式**：
```
/extract-requirements
```
啟動後，貼上原始文字，Claude 會分類整理成可直接放入 PRD 各節的結構化格式，並列出待確認的 Open Questions 和資訊缺口。

**適合的輸入材料**：
- 與客戶或使用者的訪談紀錄
- 內部需求討論會議紀錄
- 業主以 Email 或訊息傳達的需求描述
- Jira / 需求票的描述文字

---

### update-prd-status

**用途**：協助 PM 正確更新 PRD 的 YAML frontmatter 狀態與 Change History 紀錄

**使用方式**：
```
/update-prd-status
```
啟動後，告知要更新到哪個狀態、本次修改摘要，Claude 會輸出需要修改的 frontmatter 片段、Change History 新列，以及建議的 Git commit 訊息。

**狀態流程**：
```
📝 編輯中 → ✅ 內部審核通過 → 🔵 外部審核通過 → 🚧 實作中 → 🟢 已上線
```

---

### gen-flow-diagram

**用途**：將業務流程或資料流程描述轉化為 Mermaid 圖表程式碼

**使用方式**：
```
/gen-flow-diagram
```
啟動後，描述流程步驟或系統元件，Claude 會選擇適合的圖表類型（flowchart / sequenceDiagram / stateDiagram）並輸出可直接貼入 PRD 的 Mermaid 程式碼。

**支援圖表類型**：業務流程圖、資料流程圖、系統循序圖、狀態轉換圖

---

### translate-prd

**用途**：PRD 繁體中文 ↔ 英文互譯，確保醫療術語翻譯正確一致

**使用方式**：
```
/translate-prd
```
啟動後，指定翻譯方向並貼上內容，Claude 會使用統一的醫療資訊系統術語對照表進行翻譯，並在完成後提供術語確認清單。

---

## 新增 Skill

1. 在 `.claude/commands/` 目錄新增一個 `.md` 檔案（檔名即為 skill 指令名稱）
2. 在本 README 的「可用 Skills 總覽」表格新增一列
3. 在「各 Skill 詳細說明」段落補充說明
4. 提交 Pull Request，標題格式：`[Skills] 新增 skill：/skill-名稱`

**Skill 檔案撰寫原則**：
- 清楚說明 AI 的角色和任務目標
- 定義具體的執行步驟（Step 1、Step 2...）
- 提供輸出格式範例
- 包含 xHIS 領域知識（角色名稱、系統名稱、術語）
- 說明邊界：不要做什麼

---

## 更新 Skill

1. 直接修改 `.claude/commands/` 內的對應 `.md` 檔案
2. 更新本 README 的說明（若有行為變更）
3. 提交 Pull Request，標題格式：`[Skills] 更新 skill：/skill-名稱`

所有 PM 在下次 `git pull` 後即可使用更新後的版本。
