# ICERService主程式內容 (以ICER parking為例)

> 主要執行功能如下：
> Get JSON data from url parameter
> Check JSON fields
> Create XML File
> Call ICER Service


## 使用預設模組
Step 1: 啟動cgitb
```
cgitb.enable()
```

Step 2: 定義InitLog功能
```
def InitLog(tmLocationID, deviceID):
	...
	...
```
> log設定部分改成下方取得完整Reader ID後，再設定logging.getLogger('readerID')，
且放在對應Reader資料夾下

Step 3: 獲取資料 -Get url argument value
```
arguments = cgi.FieldStorage()
```

Step 4. 檢查資料 -Check argument field 'data' exist
```
try :
    jsonData = json.loads(requestData)
```
> 此處發生錯誤代碼為 908000

Step 5. 轉換json格式 -Convert json data to python dict format
```
try :
    jsonData = json.loads(requestData)
```
> 此處發生錯誤代碼為 908001

Step 6. 取得分店與裝置號碼 -Get Terminal ID & Device ID
```
tmLocationID = jsonData['TerminalID']
deviceID = jsonData['DeviceID']

readerID = tmLocationID + deviceID
```
> Terminal ID發生錯誤代碼為 908002，Device ID發生錯誤代碼為 908003

以上步驟均完成後，列印"**ICER Service Starts**"，以下步驟開始使用新創建的模組


## 使用新創模組
Step 7. 建立log檔，以及相對應的資料夾
```
InitLog(tmLocationID, deviceID)
log = logging.getLogger('ICERService')
```

Step 8. 建立Service類別 -Check service type field exist and type
```
service = ServiceType.Create(jsonData)
```
> 此處發生錯誤代碼為 908004
> 根據Service Type會讀取`ServiceClass`內不同的模組並`return`相對應的服務

Service Type包括以下：
- SignON
- Settlement
- WIFIGetRSSI
- PrintReceipt
- PrintTXNList
- WIFIConnectAck
- EZCardTXNInQuery
- EZCardEDCAPayment
- EZCardNumberQuery
- DumpSingleICERLog
- PrinterPaperStates
- EZCardEDCADataInQuery
- BatchUpdateAndCheckout

每個服務(`class Service`)均包含以下`method`，根據服務內容會再增加其他必要`method`：
- FieldCheck
- FieldState
- REQ
- RES

Step 9. 確認Reader是否有在使用
```
readerUsedState = ReaderUsed(service.filePath)
```
> 此處發生錯誤代碼為 900001

Step 10. 確認資料欄位是否存在及長度是否正確
```
service.FieldCheck()
```
> 此處發生錯誤代碼為 908005

Step 11. 特殊serviceType(非ICER交易相關)先行處理，由此執行後直接結束

ServiceType          | Error Code
-------------------- | -----------
`DumpSingleICERLog`  | None
`WIFIConnectAck` | 909980
`WIFIGetRSSI` | 909980
`PrintReceipt` | 909980
`PrintTXNList` | 909980
`PrinterPaperStates` | None
```
if service.serviceData['ServiceType'] == "WIFIConnectAck":
    if service.REQ() :
       print service.RES()
	...
	...    
readerUsedState.Release()
log.info("ICER service end \n")
exit(0)
```
Step 12. 確認Reader資料夾存在與否，建立及更新執行&資料檔。若沒問題，會回傳黑名單檔名(含副檔名)
```
blackListName = CheckReaderFolderExist.Start(service.filePath)
log.info("Black list file name : " + blackListName)
```
> 此處發生錯誤代碼為 909960

Step 13. 修改ICERINI.xml相關參數
```
comPort = service.ttyICER
xmlFilePath = service.filePath + "ICERINI.xml"
```
> 此處發生錯誤代碼為 909961

Step 14. 建立交易用XML檔
`service.REQ()`

Step 15. 啟動icerapi相關服務功能
```
print ExecuteICERAPI.Start(service)

readerUsedState.Release()
log.info("ICER service end \n")
```
