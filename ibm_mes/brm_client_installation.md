# BRM CLIENT INSTALLATION

1. 開啟MSMQ服務
 
```sh
開始 > 控制台 > 應用程式與功能 > 程式和功能 > 開啟或關閉Windows功能  
> Mircosoft Message Queue (MSMQ) 伺服器 > Mircosoft Message Queue (MSMQ) 伺服器核心 
> 勾選 MSMQ Active Directory 網域服務整合 & MSMQ Triggers (觸發程序) > 確定&靜待安裝完成。 
```

2. 拷貝BRM Client

```sh
  copy \\192.1.1.7\Saxon\BRMClient_P2 D:\BRMClient_P2
  copy D:\BRMClient_P2\mqconn.ini D:\
```

3. 設定環境變數

```sh
  我的電腦右鍵 > 內容 > 進階系統設定 > 進階 > 環境變數 > 系統變數 > 新增 : 
```

| 變數名稱             | 變數值 |
|----------------------|--------|
| LICENSED_CUSTOMER_ID | KENDA  |
| MQCONN.INI           | D      |

4. 註冊DLL

```sh
  以系統管理員執行 D:\BRMClient_P2\dll\brm_dll_20180814.cmd
```

5. 安裝Oracle Client 

```sh
  安裝32位元win32_11gR2_client
```

6. tnsnames.ora

```sh
  拷貝D:\BRMClient_P2\tnsnames.ora 並覆蓋原有的tnsnames.ora
```

7. 設定ODBC (32bit)

```sh
  運行C:\Windows\SysWOW64\odbcad32.exe 
  設定MESDBD / MESDBP / MESDBT
```

8. 運行BRMClient

```sh
  執行 D:\BRMClient_P2\BrClient.exe
```
