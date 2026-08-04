# Venus Card System v3.3.2

發佈日期：2026-08-04

本版沒有新功能，全部是效能與儲存可靠性的修正。資料 schema 不變。

## 存檔速度

- 重寫 `writeLocalSnapshot()`：整個流程只對快照序列化一次。`normalizeSnapshot()` 已驗證為冪等，因此該份字串同時就是 checksum 要算的那份，可直接重用。
- 寫入後的驗證改為比對 `writeId`（本次寫入專屬 UUID）與 checksum，取代原本「把整份快照讀回來再重算一次 SHA-256」。
- 新增 `storeProbe()`：只需確認某個欄位是否等於預期值時不再走 `storeCandidates()`，後者會對每份候選做一次完整 `JSON.stringify` 當去重簽章。
- `storeSet()` 移除 `sameStoredValue()` 的雙重序列化比對。IndexedDB 交易的 `oncomplete` 本來就代表落盤成功。
- 每次存檔的 `JSON.stringify` 由 43 次降為 5 次，SHA-256 由 3 次降為 1 次。

實測（30 人班級、桌機 Node，實機比例相近）：

| 資料量 | v3.3.1 | v3.3.2 |
|---|---|---|
| 開學初 40 KB | 16 ms | 3.3 ms |
| 期中 196 KB | 81 ms | 11.8 ms |
| 學年末 707 KB | 287 ms | 54 ms |

## localStorage 配額

- 學年末單班原本佔用約 2.8 MB（`snapshot:v4:a`、`v4:b`、`snapshot:v3`、`logs:all`、`scores:all` 互相重複）。兩個班就會超過 Safari 約 5 MB 的上限，而且撞破時是靜默失敗。
- 超過 64 KB 的值改由 IndexedDB 主責，不再鏡射進 localStorage。單班佔用降至約 36 KB。
- 保留退路：IndexedDB 不可用時（例如無痕模式），大型值仍會嘗試寫入 localStorage。

## 相容備援節流

- `snapshot:v3`／`scores:all`／`logs:all` 等 7 個 v3 分欄鍵只在 v4 讀不出來時當救援用，原本每次存檔都全量重寫。
- 改為 60 秒節流。切換到背景、離開頁面、切換班級時由 `flushCompatMirror()` 保證補寫最新內容。
- 待寫佇列以班級為單位隔離，建立新學年度班級（對另一個 classId 寫入）不會清掉目前班級尚未補寫的備援。

## 畫面與計算

- `renderOverview()` 改用 `canRedeemFrom(rem)`，不再為了算「可換幾張」而重跑一整輪 `remPoints()`（含全表掃描 `_scores`）。
- `redeemedPoints()`／`noonPoints()`／`cardCounts()` 改為單趟迴圈累加，不再用 `.filter()` 先配置中繼陣列。一次總覽渲染少配置 90 個陣列。
- 星雲主題：移除標題列的 `backdrop-filter` 與 `nebula-glow` 無限動畫。該動畫改變的是 `box-shadow`（paint 屬性，非合成層屬性），固定元素每幀重繪加上重算毛玻璃是 iPhone 捲動掉幀的主因。改用高不透明度背景與靜態光暈，外觀差異極小。
- 星雲主題：移除 `.card`、`.stat`、`.class-dropdown` 的 `backdrop-filter`。這些元素會在長頁面上重複出現，等於每個都是一個獨立模糊圖層。
- 修正一條位於主題區塊之後的重複 `[data-theme="nebula"] .app-header`，它原本會反過來把毛玻璃覆蓋回去。
- 移除 6 條內容完全相同的重複 CSS 規則，以及已無使用者的 `@keyframes nebula-glow`。

## 實機效能探針

- 網址加上 `?perf=1` 會在畫面上方顯示每次存檔的實際毫秒數、平均與最久耗時，以及 localStorage 即時佔用量。點一下面板可關閉。
- 未加參數時所有量測程式碼都不會執行。

## 相容與保護

- 資料 schema 不變，可直接沿用 v3.3.1 的全部本機與雲端資料。
- 雙向相容：v3.3.1 寫的資料 v3.3.2 讀得出來；v3.3.2 寫的資料 v3.3.1 也讀得出來，必要時可安全回滾。
- 雙槽（a/b）原子提交、checksum 完整性檢查、雙後端冗餘全部維持不變。
- Venus OS／LIMU 的 `users/{uid}/data` 路徑維持嚴格唯讀，本版未新增任何寫入。
- 快取與建置識別升級至 v3.3.2。

## 測試

- 儲存回歸 23 項全數通過：讀寫一致性、新舊版雙向互通、雙槽交替與世代遞增、四種損毀情境、備援節流正確性、跨班佇列隔離、配額檢查。
- 點數計算等價測試 20 組、逐座號比對 355 次結果完全相同，涵蓋 null 缺考、undefined、字串分數、NaN 等邊界值。
- `normalizeSnapshot` 冪等性 4 組資料驗證通過（這是重用 checksum 的正確性前提）。
- 語法檢查、ESLint（0 errors）、inline handler 交叉比對、CSS 括號平衡、`BUILD_ID` 與 `sw.js` 一致性檢查全部通過。
