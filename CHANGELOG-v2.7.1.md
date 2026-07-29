# Venus Card System v2.7.1

## iOS 26.5.2 底部大空白

- 將 Home Screen Web App 狀態列模式由 `black-translucent` 改為
  `black`。
- 避開 iOS 26.5.2 將頂部狀態列高度錯留在底部、且該區域無法由 DOM
  繪製或接收觸控的 WebKit 問題。
- 保留 52px 底部導覽、最多 4px 緩衝與至少 44px 的按鍵觸控高度。
- 不再使用 `screen.height` 強行擴張 App，避免五個底部按鍵再次被裁掉。

## 保留項目

- 慕夏・藍紫鳶尾、日間、NERV、星雲四套主題。
- Firebase、資料格式、同步、救援、抵免診斷與更新保護。
- 離線 Excel、完整分析、家長報表、新 Icon 與開場圖。
