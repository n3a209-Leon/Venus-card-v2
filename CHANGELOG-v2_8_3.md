# Venus Card System v2.8.3

## 作業抵免（修正顯示為 0）

- Venus OS 的 day 文件一律以 `classId` 命名，改以 `classId` 作為唯一主前綴。
- 不再從 `cls.year` + `cls.className` 反推前綴；該欄位可在 Venus OS 隨時修改，
  一改就會使抵免全部歸零。
- `{年}_{班名}` 降為備援，僅在主前綴掃不到任何文件時啟用，且逐一嘗試、命中即停。
- 刻意不做前綴聯集：`{年}_{班名}` 不含屆別，聯集可能把去年同名班級的抵免併入。

## 掃描日期範圍

- `academicRangeForClass()` 改以 `classId` 開頭的年份計算學年，`cls.year` 僅作備援。
- 修正 2026-08-01 之後的抵免落在掃描窗外、無法統計的問題。
- 前後各一年的 padding 用於吸收「classId 年份為學年起始年或結束年」的歧義，
  請勿收緊。

## Card 自建班級

- `source` 非 `venus-os` 的班級不再查詢 Venus OS，直接視為無抵免。
- 避免備援前綴誤中同名的 OS 班級。

## 抵免診斷

- 新增「班級來源」與「主前綴」兩行，可直接看出主前綴是否命中。
- 診斷改為優先掃描 `classId` 主前綴，再掃候選年度。
- 「含抵免」改為只計算真正有抵免紀錄的日期；空的 `{}` 不再計入。
- 抵免樣本只挑真的有抵免的日期，不再出現看似資料清空的 `→ {}`。

## 保護範圍

- Venus OS 維持唯讀，未新增任何寫入、更新或刪除。
- 總覽個別座號資訊、欄位與計算邏輯未修改。
- 獎卡與成績資料格式、Firebase 同步、救援、Excel、報表、班級管理未修改。
- 底部導覽尺寸與四套主題未修改。

## 已知待辦（未包含於本版）

- `classCloudDocId()` 仍為 `{cls.year}__{classId}`，Venus Card 自己的雲端文件
  路徑依然綁在可修改的 `cls.year` 上。建議另開一版，將正常載入改為沿用
  `collectRecoveryCandidates()` 的 `where('classId','==',classId)` + 
  `mergeVisibleSnapshots()`，使文件 ID 不再影響讀取。
