# Informix CDSK Installation

##  準備材料 informix、linux、odbc、isql

### Step 1 :
下載 **clientsdk.3.50.FC2DE.LINUX.tar(Linux system for x86-64,RedHat)** ，上傳到伺服器,  解壓 `tar -xvf clientsdk.3.50.FC2DE.LINUX.tar`

### Step 2 :
安裝, `./installclientsdk` 如果報錯，請使用如下命令 `./installclientsdk -javahome none`。

### Step 3 :
選擇安裝目錄, 這個可以看自己的習慣，這裡使用如下目錄， /opt/IBM/informix。

### Step 4:
編輯**/etc/services**，文件尾添加informix映射埠，格式為：
`埠名稱 埠/協議  #Informix Server`，例：`on12tcp 3002/tcp`。

### Step 5 : 
切換到$INFORMIXDIR/etc，編輯**sqlhosts** (若無此檔需自行新增)，在文件中添加一個新的資料庫連接，格式為：`資料庫名稱 tcp協議 伺服器地址(或名稱) 埠名稱(或埠號)`，例`ids12 onsoctcp kenda on12tcp`。 

> 注意: 若使用**伺服器名稱**需在/etc/hosts添加伺服器，格式為 `ip 伺服器名稱`，例: `192.1.1.1 kenda`

### Step 6 :
編輯`/etc/odbc.ini`，添加DNS設定，例：

    ;---------------------------------------------------------------------------
    ; IBM INFORMIX ODBC Sample File
    ; File: odbc.ini
    ;---------------------------------------------------------------------------
    [ODBC Data Sources]
    erp =IBM INFORMIX ODBC DRIVER  # 此行不加似乎沒影響, 只做說明用。

    ;---------------------------------------------------------------------------
    ; Define ODBC Database Driver's Below - Driver Configuration Section
    ;---------------------------------------------------------------------------
    [erp]
    Driver=/opt/IBM/informix/lib/cli/iclit09b.so
    Description=IBM INFORMIX ODBC DRIVER
    Database=erp
    LogonID= informix
    pwd= in4mix
    Servername=ids12               #等同sqlhosts裡的名稱, $INFORMIXSERVER須設定一致
    CursorBehavior=0
    CLIENT_LOCALE=zh_TW.big5
    DB_LOCALE=zh_TW.big5
    TRANSLATIONDLL=/opt/IBM/informix/lib/esql/igo4a304.so
    
    ;---------------------------------------------------------------------------
    ; UNICODE connection Section
    ;---------------------------------------------------------------------------
    [ODBC]
    ;uncomment the below line for UNICODE connection
    UNICODE=UCS-2

    ;---------------------------------------------------------------------------
    ; Trace file Section
    ;---------------------------------------------------------------------------
    Trace=0                             # 1 = enable 0 = disable
    TraceFile=/tmp/odbctrace.out
    InstallDir=/opt/IBM/informix
    TRACEDLL=idmrs09a.so 

### Step 7:
編輯`/etc/odbcinst.ini`，添加instance設定，例：
  

    ;---------------------------------------------------------------------------
    ; IBM INFORMIX ODBC Sample File
    ; File: odbcinst.ini
    ;---------------------------------------------------------------------------
    [IBM INFORMIX ODBC DRIVER]
    Driver=/opt/IBM/informix/lib/cli/iclit09b.so
    Setup=/opt/IBM/informix/lib/cli/iclit09b.so
    APILevel=1
    ConnectFunctions=YYY
    DriverODBCVer=03.51
    FileUsage=0
    SQLLevel=1
    smProcessPerConnect=Y
    [ODBC]
	; Trace file Section
	Trace=Yes              # Yse = enable No =disable
	TraceFile=/tmp/odbcinst_trace.out

### Step 8 :

編輯`/etc/profile`添加環境變量：

    export INFORMIXDIR=/opt/IBM/informix
    export ODBCINI=/etc/odbc.ini
    export ODBCINSTINI=/etc/odbcinst.ini
    export ODBCSYSINI=/unixODBC/etc
    export PATH=$PATH:$INFORMIXDIR/bin
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$INFORMIXDIR/lib:$INFORMIXDIR/lib/esql

### Step 9 :
測試連線，`下面erp為odbc.ini中[erp]的erp`

    $$ isql erp -v 回車，出現：
    +---------------------------------------+
    | Connected!                            |
    |                                       |
    | sql-statement                         |
    | help [tablename]                      |
    | quit                                  |
    |                                       |
    +---------------------------------------+
    SQL>

然後就可以進行操作了。

具體參見：
1. How To Setup Generic Connectivity - HSODBC - on Linux
2. https://www.taodabai.com/how/610181466.html
3. https://techblog.jj-it.de/oracle-gateway-to-odbc/access-to-informix-database-informix-odbc-driver-installation/