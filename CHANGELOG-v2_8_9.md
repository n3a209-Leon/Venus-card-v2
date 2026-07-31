# Venus Card System v2.8.9

## 座號自動帶入（修正新班級一片空白）

- Venus OS 班級在 Venus Card 沒有座號時，依 `settings:main.classes[].classSize`
  自動帶入 1–N 號。
- 只在座號完全空白時執行，絕不覆蓋既有名單。
- 在 `loadClass()` 完成之後才執行，避免用較新的時戳蓋掉雲端既有名單。
- Card 自建班級、人數為 0 或超過 60 時不帶入。

此為幹部／組長／秘書長顯示為空的根本原因：資料已讀取成功，但三份名單都以
`_nos.includes(no)` 過濾，座號為空時全部被濾掉。

## 分組改讀 groupVersions

- LIMU 已改用 `groupVersions:{classId}`，結構為
  `{ versions:[{effectiveDate,verId,groups:[…]}] }`。
- 依 `effectiveDate` 選出已生效且最新的一版；皆未生效時退回最早一版。
- 舊的 `hw5ren:groups:{classId}` 保留為備援。
- 移除載入流程中重複的分組讀取。

## 學期判斷修正

- `semStart2` 早於 `semStart1` 時視為上一學年殘留值並忽略。
  先前在暑假會被誤判為下學期（例：`semStart1` 2026-08-31、
  `semStart2` 仍為 2026-02-23）。
- 判斷出的學期沒有資料、另一學期有資料時，自動改顯示有資料的那一個。

## 保護範圍

- Venus OS 與 LIMU 資料維持唯讀：`officers:*`、`groupVersions:*`、`classes`
  均只使用 `.get()`，無任何寫入。
- 總覽個別座號資訊、欄位與計算邏輯未修改。
- 發卡紀錄仍為 `k:'幹部'`，`cardCounts()` 與總覽欄位不受影響。
