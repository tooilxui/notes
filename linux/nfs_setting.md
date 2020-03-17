# 1、建立/etc/exports文件

    [root@db1 ~]# vi /etc/exports
    /u02/share -sec=sys:krb5p:krb5i:krb5:dh,rw,root=192.168.1.10
    
    /u02/share：共享目錄
    -sec：是指資料加密方法
    root=：設定192.168.1.10主機對/u02/share有讀寫許可權；
           如果root後面指定的是主機名，則必須將主機名稱和IP寫入/etc/hosts文件，否則無法解析（通過DNS解析可以忽略）

# 2、更新/etc/xtab文件

    [root@db1 ~]# exportfs -a
    
    [root@db1 ~]# more /etc/xtab
    /u02/share -sec=sys:krb5p:krb5i:krb5:dh,rw,root=192.168.1.10
# 3、啟動nfs相關服務

    [root@db1 ~]# startsrc -g nfs
    0513-059 The biod Subsystem has been started. Subsystem PID is 9371710.
    0513-059 The nfsd Subsystem has been started. Subsystem PID is 11403630.
    0513-059 The rpc.mountd Subsystem has been started. Subsystem PID is 6291686.
    0513-059 The nfsrgyd Subsystem has been started. Subsystem PID is 8913388.
    0513-059 The gssd Subsystem has been started. Subsystem PID is 11141424.
    0513-059 The rpc.lockd Subsystem has been started. Subsystem PID is 11141426.
    0513-059 The rpc.statd Subsystem has been started. Subsystem PID is 4915606.
    
    [root@db1 ~]# startsrc -g portmap
    0513-059 The portmap Subsystem has been started. Subsystem PID is 3670366.
    
    [root@db1 ~]# showmount -e localhost
    export list for localhost:
    /u02/share (everyone)
# 4、客戶端掛載

    [root@hisdb ~]# showmount -e db1
    Export list for db1:
    /u02/share (everyone)
    
    如果客戶端是linux系統，則會出現下面錯誤，AIX客戶端直接掛載即可。
    [root@hisdb ~]# mount db1:/u02/share /mnt
    mount.nfs: Remote I/O error
    
    解決辦法如下，加vers引數並指定為3即可。
    [root@hisdb ~]# mount -o vers=3 db1:/u02/share /mnt