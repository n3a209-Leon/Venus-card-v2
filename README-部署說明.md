# Venus Card System v2.6.1 部署說明

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
