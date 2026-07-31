# Venus Card System v2.8.4

## 幹部／組長／秘書長分成三個區塊

- 每週給卡頁拆成三個獨立名單，各有自己的週給按鈕與人數提示。
- 三個區塊都沿用原本的點擊規則：一下 ×1 綠、再一下 ×2 橘、第三下取消。
- 發卡紀錄一律記為 `k:'幹部'`，`cardCounts()` 與總覽欄位完全不變；
  來源改由 `note`（幹部週給／組長週給／秘書長週給）與新欄位 `wt` 區分。
- 重複發放的判斷改看 `wt`，三個區塊各自獨立，不會互相誤擋。
  舊紀錄沒有 `wt`，一律視為幹部，相容性不受影響。

## 自動帶入 Venus OS 分組

- 組長與秘書長自 `hw5ren:groups:{classId}` 的 `leader` / `secretary` 自動帶入，
  每人 ×1。
- 只在該班首次載入時帶入一次，之後尊重手動調整，不再覆蓋
  （以 `_fixed.roleSeeded` 記錄，隨 fixed 欄位同步）。
- 兩區塊各有「↻ 重新帶入」按鈕，覆蓋前會先確認，並可撤銷。
- 不在座號名單內的組長／秘書長會自動略過。
- 幹部區塊的角色小字保留，「副」改為「秘書長」。

## Venus OS 資料掃描（新增）

- 更多 → 🔎 Venus OS 資料掃描。
- 列出本班在 `users/{uid}/data` 底下的所有文件與最上層欄位，
  每日文件會收合成份數，另列出目前分組的欄位與組長／秘書長人數。
- 純唯讀，用於確認 Venus OS 是否還有其他幹部紀錄。

## 死碼清除

- 移除 `auth-checking` 兩處不存在的元素參照。
- 移除 `updateReportProgress()` 內永不執行的彈出視窗分支，
  簽章由 `(done,total,no,w)` 改為 `(done,total,no)`。
- 移除未使用的 `_reportBuildPopup`。

## 資料相容

- 舊快照只有 `cadre` 與 `toilet` 時，會自動補上空的 `leader` 與 `secretary`，
  原有名單完整保留；陣列格式的舊 `cadre` 仍可轉換。
- 空資料防護（`hasVisibleData` / `hasMeaningfulData` / 救援摘要）已納入
  新的兩份名單，只設定組長或秘書長的快照不會被誤判為空。
- 修改座號名單時，三份名單都會一併清除已不存在的座號。

## 保護範圍

- Venus OS 維持唯讀，未新增任何寫入、更新或刪除。
- 總覽個別座號資訊、欄位與計算邏輯未修改。
- Firebase 同步、救援、Excel、報表、班級管理、底部導覽與四套主題未修改。

## 已知待辦（未包含於本版）

- `classCloudDocId()` 仍為 `{cls.year}__{classId}`，建議另開一版改為
  `where('classId','==',classId)` + `mergeVisibleSnapshots()`。
