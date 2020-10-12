# ICASH2Service 主程式流程

> 主要執行功能:<br>
> Get JSON data from url parameter<br>
> Check JSON fields<br>
> Call ICASH Service

## 初期步驟
Step 1. 定義`switch`物件
```
class switch(object):
    def __init__(self, value):
        self.value = value
        self.fall = False
	...
	...
```

Step 2. 啟動cgitb `cgitb.enable()`

step 3. 定義`SetjsonData`功能
```
ttyApiPort = Tools.GetPrivateProfileString(...)
ttyServicePort = Tools.GetPrivateProfileString(...)
...
...

jsonData['Log'] = log
jsonData['Tools'] = Tools
...
...
```

Step 4. 取得url參數 -Get url argument value
```
try :
    requestData = sys.stdin.read()
    # requestData=arguments['data'].value
...
...
```
> 此處錯誤代碼為**9990000001**

Step 5. 將取得的json轉為Python `dictionary`
```
jsonData = json.loads(requestData)
```
> 此處錯誤代碼為**9990000002**

Step 6. 取得分店ID和裝置ID -Get TerminalID DeviceID
```
TerminalID = jsonData['TerminalID']
DeviceID = jsonData['DeviceID']
```
> 分店ID無法取得的錯誤代碼為**9990000003** <br>
> 裝置ID無法取得的錯誤代碼為**9990000004**

Step 7. 啟用log設定並建立讀卡機資料夾 -Initail Log setting and create reader folder
```
SystemLogDir = LogPath + TerminalID + "/" + \
                   DeviceID + "/" +"SystemLog/"

    if not os.path.exists(SystemLogDir):
        os.makedirs(SystemLogDir)
        os.chmod(SystemLogDir, 0o777)
		...
		...
```
> 此處若發生錯誤代碼為**9990000005**

Step 8. 啟動`ErrorTable`:
```
ErrorTable.DataInit(log)
```
> 完成後顯示 "iCash service Start >>>"

Step 9. 呼叫`SetjsonData(jsonData)`

<br>
## 檢查必要欄位並建立服務、執行服務
step 10. 建立服務`service`類別 -Check service type field exist and type
```
service = ServiceType.Create(jsonData)
```
> 此處發生錯誤代碼為**9980000001**

Step 11. 確認Reader是否有在使用
```
readerUsedState = ReaderUsed(service)
```
> 此處發生錯誤代碼為**9980000002**

Step 12. 確認資料欄位是否存在及長度是否正確
```
if service.FieldCheck() is False:
    readerUsedState.Release()
```
> 此處發生錯誤代碼為**9980000003**

Step 13. 呼叫`switch`物件並執行以下各項功能：
- 確認讀卡機資料夾存在與否，建立及更新執行&資料檔
- 若沒問題，會回傳黑名單檔名(含副檔名)
```
for case in switch(service.ServiceType):
	if case("TermAuth") or case("GetApVersion") or ...
	...
	...
		CheckFile = CheckFile(service)
		service.BlackListName = CheckFile.BlackListName
```
> 此處發生錯誤代碼為**9980000004**

- 設定相關參數
```
		SettingINI = SettingINI(service)
```
> 此處發生錯誤代碼為**9980000005**

- 啟動API相關服務
```
        ...
		executeAPI = ExecuteAPI(service)
        service.jsonData = executeAPI.StartFlow()
		...
		readerUsedState.Release()
        log.info("iCash service End <<<\n")
        sys.exit(0)
```

- 無ServiceType交易
```
    if case():
        readerUsedState.Release()
```
> 同時回覆錯誤代碼**9980000000**
