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

### cgi.FieldStorage()
建立FieldStorage的實例(instance)
```
import cgi, cgitb

form = cgi.FieldStorage()
```

### ReaderUsed
讀取讀卡機所在目錄與dat檔，確認讀卡機是否有在使用；
若沒有，將使用狀態改為使用中並寫入dat檔，最後回傳`True`
```
log.info("Check reader used state ...")
self.Get()
...
log.info("Reader not used !")
return self.Set("1")
...
log.info("Setting reader in use state ...")
readerState.write(data)
...
log.info("Setting done !")
return True
```

### ServiceType
只有一個function，查看要用的服務並返回`ServiceClass`內各項服務的物件。
- `SignON` : 含`FieldCheck`、`FieldState`、`DecryptoAndUnpack`、`PackAndCrypto`、`REQ`、`RES`、`CheckAutoTopUpAmount`
- `Refund` : 含`FieldCheck`、`FieldState`、`DecryptoAndUnpack`、`PackAndCrypto`、`REQ`、`RES`、`CheckAutoTopUpAmount`<br>
.<br>
.<br>
.


### DongleDataStruct
將傳入字串形態的整數轉成16進制字串存下
