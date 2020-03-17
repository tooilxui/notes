# 資料庫整理 (dbexport , dbimport)

### 資料匯出 - dbexport
1.  資料匯出前應先使用onstat -d  計算資料庫現有使用空間 (或簡單看一下/unload資料夾的大小) ， 並確認資料庫匯出的目標資料夾空間足夠存放。
2.  確認欲匯出之資料庫存放的dbspace name 
    ```
        dbaccess 進入 dbaccess 選單 > Database > dBspace > Select > DatabasseName > Info > dBspace ，顯示如下：
        
        DATABASE INFO:   dBspace  Nls  Routine  Databases  Exit
        Display DBSPACE information for a database.
        
        ----------------------- erp@ids12 -------------- Press CTRL-W for Help --------
        
                                        Number of   When
           Id    Name                    Chunks    Created       Mirror
         
            8    datadbs                     35    02/19/2014    N
    ```
3.  匯出需獨佔存取資料庫，故需將UNIX設定為nologin、或者資料庫設定為single user，並將使用者踢出。
    * unix nologin:
        ``` 
            <root:/> echo 'UNIX系統維護中，請稍後登陸。' > /etc/nologin
        ```
        取消nologin
        ```
            <root:/> rm /etc/nologin
        ```
        ref: [AIX login command](https://www.ibm.com/support/knowledgecenter/en/ssw_aix_71/com.ibm.aix.cmds3/login.htm)
        

    * informix single user
        僅開放informix登入資料庫
        ```
            <informix:> onmode -j 
        ```
        或 指定特定帳號可登入資料庫
        ```
            <informix:> onmode -j -U saxon,informix,root
        ```
        取消single user mode 
        ```
            <informix:> onmode -m
        ```
        ref:  [Informix Change database server mode](https://www.ibm.com/support/knowledgecenter/en/SSGU8G_11.70.0/com.ibm.adref.doc/ids_adr_0422.htm)
4.  匯出
    * 指令如下，開始匯出後會在指定目錄下方產生dbexport.out記錄檔，可透過紀錄檔確認匯出是否完成(有completed字樣)，其中Dbspace name為欲匯出之資料庫名稱，/ExportPathName為匯出時指定的資料夾路徑：
        ```
            <informix:/> dbexport DatabaseName -c -ss -q -o /ExportPathName
            dbexport completed
        ```
5. 刪除資料庫
    *   進入dbaccess 並 drop database 
    
### 資料匯入 - dbimport
1.  指令如下，其中DbspaceName為匯出時確認之Dbspace name，/ExportPathName為匯出時指定的資料夾路徑：
    ```
        dbimport erp -c -q -d DbspaceName -i /ExportPathName 
    ```

ref:

[Syntax of the dbexport command](https://www.ibm.com/support/knowledgecenter/en/SSGU8G_12.1.0/com.ibm.mig.doc/ids_mig_116.htm)

[Syntax of the dbimport command](https://www.ibm.com/support/knowledgecenter/en/SSGU8G_12.1.0/com.ibm.mig.doc/ids_mig_123.htm)