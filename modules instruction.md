# 各模組功能

### cgitb
最初式設計來顯示html格式的cgi指令碼(script)詳細的追蹤資料。如果發生未經處理的異常，將會顯示詳細的制式報告。可透過函式的參數將報告顯示在瀏覽器中，或是把報告紀錄到文件供日後分析。
```
cgitb.enable(display=1, logdir=None, context=5, format="html")
```
- `display` : 預設值1，改為0禁止報告發送至瀏覽器
- `logdir` : 寫入報告的文件目錄
- `context` : 預設值5，目前追蹤資料源代碼顯示的上下文數量
- `format` : 預設值"html"，改為其他任何數值會改用純文字輸出

