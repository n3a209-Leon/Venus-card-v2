# Venus Card System v2.6.4 部署說明

本版修正 v2.6.3 在 iOS 26.5.2 主畫面模式中把 App 強制放大到
`screen.height`，造成底部五個導覽按鍵被排到 WebKit 可視範圍之外的問題。

修正後：

- 五個底部導覽按鍵會固定留在可見畫面內。
- App 只使用 iOS WebKit 真正可繪製的高度，不再將導覽列推到畫面外。
- 底部導覽本體縮為 48px，按鍵仍保有至少 44px 的可點擊高度。
- 系統保留區使用與 App 相同的深色背景，減少視覺斷層。
- iOS 26.5.2 主畫面模式額外保留的系統區不屬於網頁可操作範圍，App 不會再嘗試把按鍵放進該區。
- Firebase、資料格式、同步、救援、診斷、Excel 與報表功能均未更動。

請將壓縮檔內的全部檔案一起上傳到網站根目錄：

- `index.html`
- `sw.js`
- `xlsx.full.min.js`
- `manifest.json`
- `splash-v2.6.1.jpg`
- `venus-icon-180-v2.6.1.png`
- `venus-icon-192-v2.6.1.png`
- `venus-icon-512-v2.6.1.png`

部署後，舊版 App 會顯示「新版本已準備好」：

1. 可以按「稍後」繼續目前操作。
2. 按「立即更新」時，App 會先保存本機資料再重新整理。
3. 報告產生中不會允許重載；Excel 預覽或快速給卡尚未完成時會先提醒。

`xlsx.full.min.js` 是本機版 SheetJS 0.18.5，已加入離線快取；請勿省略，否則 Excel 匯入功能無法使用。

`LICENSE.xlsx.txt` 為 SheetJS 授權文件，保留在部署包中供查閱。

本版以直式女神圖作為全螢幕開場，以方形女神近景作為 App Icon。
若 iPhone 主畫面仍顯示舊圖示，請移除原本的主畫面捷徑，再從 Safari 重新「加入主畫面」。
