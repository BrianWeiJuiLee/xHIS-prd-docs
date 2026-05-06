# xHIS PRD 文件管理空間

本 Repository 為 xHIS 產品團隊的正式 PRD（產品需求規格書）存檔空間，供研發人員參考實作使用。

## 系統目錄

| 系統代碼 | 系統名稱 | 負責 PM | 資料夾 |
|---------|---------|--------|-------|
| Register | 掛號系統 | — | [Register/](./Register/) |
| Billing | 批價系統 | — | [Billing/](./Billing/) |
| Declare | 申報系統 | — | [Declare/](./Declare/) |
| Referral | 轉診系統 | — | [Referral/](./Referral/) |
| Pharmacy | 藥局系統 | — | [Pharmacy/](./Pharmacy/) |
| OPD | 門診醫令系統 | — | [OPD/](./OPD/) |
| ER | 急診醫令系統 | — | [ER/](./ER/) |
| IPD | 住院醫令系統 | — | [IPD/](./IPD/) |
| ERNIS | 急診護理系統 | — | [ERNIS/](./ERNIS/) |
| IPDNIS | 住院護理系統 | — | [IPDNIS/](./IPDNIS/) |
| Portal | 首頁系統 | — | [Portal/](./Portal/) |

## PRD 狀態定義

| 狀態 | 說明 |
|-----|-----|
| 📝 編輯中 | PRD 正在撰寫或修訂，尚未完成 |
| ✅ 內部審核通過 | 已通過 PM 團隊內部審查，內容凍結 |
| 🔵 外部審核通過 | 已通過客戶或主管機關確認，可進入實作 |
| 🚧 實作中 | 研發團隊已接手，正在開發中 |
| 🟢 已上線 | 功能已部署至正式環境 |

## AI Skills｜PRD 輔助工具

本 repo 內建 AI Skill，搭配 [Claude Code](https://claude.ai/code) 使用，clone repo 後即可在 Claude Code 中以指令呼叫。

| Skill 指令 | 用途 |
|-----------|-----|
| `/draft-prd` | 從功能描述起草完整 PRD 草稿 |
| `/review-prd` | 六維度 PRD 品質審查 |
| `/gen-test-cases` | 從 Use Cases 自動產生 Test Cases |
| `/extract-requirements` | 從會議紀錄 / 訪談稿萃取結構化需求 |
| `/update-prd-status` | 更新 PRD 狀態與 Change History |
| `/gen-flow-diagram` | 產生 Mermaid 業務流程圖 / 資料流程圖 |
| `/translate-prd` | PRD 繁中 ↔ 英文互譯 |

詳細說明與使用方式請參閱 [skills/README.md](./skills/README.md)。

---

## 使用方式

- **新增 PRD**：參考 [templates/PRD-template.md](./templates/PRD-template.md)，在對應系統資料夾建立新檔案，提交 Pull Request
- **更新 PRD**：修改對應檔案後提交 Pull Request，並在 PR 說明欄位標註版本異動內容
- **查閱 PRD**：進入各系統資料夾，參考 `README.md` 中的 PRD 清單與狀態
- **命名規則**：`PRD-[功能名稱].md`，例如 `PRD-PatientSearch.md`

詳細貢獻流程請參閱 [CONTRIBUTING.md](./CONTRIBUTING.md)。
