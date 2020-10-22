# ICASHService 修改內容

## ServiceType.py
1. 去除`class` Switch

## ICASH2Service.py (主程式)
1. 刪除程式內沒有使用的`import`模組
1. 訂正格式，使格式符合PEP8規範
> - 空白行數、空白空格數
> - 刪除空白的註解行
1. 訂正格式，符合IDE建議寫法
> - `if jsonData.has_key('TerminalID')...` --> 
> `if 'TerminalID' in jsonData...`
1. 把建立log、建立log資料夾的程式改為function：`init_log(log_path)`
1. 刪除非交易類服務

