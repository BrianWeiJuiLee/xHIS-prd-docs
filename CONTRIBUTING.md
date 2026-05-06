# PRD 貢獻指南

## PRD 生命週期與審核流程

```
編輯中 → 內部審核通過 → 外部審核通過 → 實作中 → 已上線
```

### 各階段說明

1. **編輯中**：PM 在自己負責系統的資料夾內建立或修改 PRD 檔案，開立 Draft Pull Request
2. **內部審核通過**：PR 指定至少一位其他 PM 進行 Review，Approve 後更新 PRD 檔頭狀態，標記 PR 為 Ready for Review 並 Merge
3. **外部審核通過**：客戶或主管機關確認後，PM 更新 PRD 檔頭狀態並 Push
4. **實作中**：PRD 交付研發，PM 更新狀態
5. **已上線**：功能上線後，PM 更新狀態

## 建立新 PRD

1. 從 `templates/PRD-template.md` 複製至對應系統資料夾
2. 檔名格式：`PRD-[功能名稱].md`（英文，使用 PascalCase，例如 `PRD-PatientSearch.md`）
3. 填寫檔頭 metadata（狀態設為「編輯中」）
4. 完成後開立 Pull Request，PR 標題格式：`[系統代碼] 新增 PRD：[功能名稱]`

## 更新現有 PRD

- 修改 PRD 內容後，務必更新檔頭的 `更新日期` 與 `版本`
- 在 `修訂紀錄` 區塊補充本次修改摘要
- PR 標題格式：`[系統代碼] 更新 PRD：[功能名稱] v[版本號]`

## Pull Request 規範

- PR 必須指派對應系統的 PM 負責人作為 Reviewer
- 非 Draft PR 需至少 1 個 Approve 才能 Merge
- Merge 方式統一使用 Squash and Merge
- 不得直接 Push 至 `main` branch

## 系統資料夾負責人

每位 PM 只能修改自己負責系統的資料夾，跨系統修改需另行知會。如需更動 `templates/` 或根目錄文件，須知會所有 PM。
