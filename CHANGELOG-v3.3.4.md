# Venus Card System v3.3.4

發佈日期：2026-08-04

延續 v3.3.2 的效能修正。本版處理 nerv 主題的同類問題、修掉點卡熱路徑上的全資料深拷貝，並擴充實機探針。資料 schema 不變。

## 撤銷點不再深拷貝整份資料

- `pushUndo()` 原本每次都呼叫 `currentSnapshot()`，那是對整個學期資料做 `JSON.parse(JSON.stringify())` 的深拷貝。
- 它掛在「快速給卡」這類熱路徑上，而且**沒有防抖**——`persistLocal` 有 250ms 防抖可以合併連點，`pushUndo` 每點一次就跑一遍，且是同步的，直接加在點卡的反應時間上。
- 改為存序列化字串（`snapshotText()`）：序列化只跑一趟，撤銷時才解析。
- 撤銷堆疊常駐的內容也從物件圖變成字串，記憶體大幅下降（學年末 5 份深拷貝物件在記憶體中可達數十 MB）。

實測（30 人班級、桌機 Node，5 輪取中位數）：

| 資料量 | v3.3.3 | v3.3.4 |
|---|---|---|
| 開學初 40 KB | 0.7 ms | 0.3 ms |
| 期中 196 KB | 3.0 ms | 1.6 ms |
| 學年末 708 KB | 12.7 ms | 8.0 ms |

- `?perf=1` 新增「建立撤銷點」的量測項。

## nerv 主題的捲動成本

- `data-theme` 是設在 `document.documentElement` 上，而 v3.3.2 以前 `[data-theme="nerv"]` 帶著 `animation:flicker 8s infinite`，等於**在根元素上做 opacity 動畫**——整份文件會反覆進出合成層並全頁重繪，永不停止。這與 v3.3.2 修掉的星雲主題標題列是同一類問題，但影響範圍更大。
- 改成只讓固定的掃描線疊層（`[data-theme="nerv"]::before`，`pointer-events:none`、獨立圖層）閃動。CRT 質感保留，主要內容完全不受影響。
- keyframes 改為掃描線在瞬間加深（`opacity .85 → 1`），維持原本「畫面短暫變暗」的觀感。
- `prefers-reduced-motion` 的關閉目標同步改為 `::before`。

## 實機效能探針擴充

- `?perf=1` 除了存檔，現在也量測總覽渲染與建立撤銷點。面板分別顯示各項的平均與最久耗時。
- 面板加上目前主題名稱，方便比較不同主題下的實際差異。

## CSS 整理

- 合併星雲主題重複的 `.cd-item:hover` / `.cd-item.active`，寫法與 mucha、odyssey、預設主題一致。（選單項目本來就有「✓ 使用中」徽章標示目前班級，底色深淺是多餘的。）
- 同脈絡下內容完全相同的冗餘規則清為 0。

## 相容與保護

- 資料 schema 不變，可直接沿用 v3.3.2 的全部本機與雲端資料。
- 儲存、同步與計算邏輯未改動；撤銷點的變更已驗證與舊版逐字元等價。
- Venus OS／LIMU 的 `users/{uid}/data` 路徑維持嚴格唯讀。
- 快取與建置識別升級至 v3.3.4。

## 測試

- v3.3.2 的全部回歸測試在本版重跑通過：儲存 23 項、點數等價 20 組（逐座號 355 次）、冪等性 4 組。
- 新增 `test-undo.js`：驗證 `snapshotText()` 產生的字串與舊版 `JSON.stringify(currentSnapshot())` 逐字元完全相同、還原出的是獨立副本不共用參考，並附成本對照。
- 語法檢查、ESLint（0 errors）、inline handler 交叉比對、CSS 括號平衡、`BUILD_ID` 與 `sw.js` 一致性通過。
- 新增檢查：確認根元素不再帶動畫、`flicker` 確實掛在 `::before`。

## 已知待辦（本版未處理）

- 星雲主題 `.card::after` 使用 `filter:drop-shadow`，每張卡片一個濾鏡圖層。影響小於毛玻璃，但仍是重複元素上的合成成本，尚未量測。
- 17 組「後者覆蓋前者」的重複選擇器多數是刻意寫法（群組規則加個別補充、`isolation:isolate` 附加），少數是完全被蓋掉的死宣告（如 `[data-theme="nebula"] .tab-btn.active` 第一條）。無效能影響，清理需逐條對畫面，未動。
- 雲端同步 `syncSnapshotToCloud()` 的 legacy 分支一次做 7 次 `JSON.stringify`，寫法與 v3.3.2 修掉的本機路徑同款。不在點卡熱路徑上，尚未量測。
