- 確認server 端 ssh 服務狀態是否為running，若沒有ssh服務則下 `  sudo apt-get update && sudo apt-get install -y openssh-server` 安裝
  ```
     $ service ssh status 
	 sh: 0: getcwd() failed: No such file or directory
     ● ssh.service - OpenBSD Secure Shell server
        Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
        Active: active (running) since Thu 2019-09-12 10:33:19 CST; 15min ago
       Process: 8653 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
      Main PID: 8660 (sshd)
         Tasks: 1 (limit: 2362)
        CGroup: /system.slice/ssh.service
                └─8660 /usr/sbin/sshd -D
     
     Sep 12 10:33:32 ck4.kenda.com.tw sshd[8680]: pam_unix(sshd:session): session opened for user saxon by (uid=0)
     Sep 12 10:33:47 ck4.kenda.com.tw sshd[8798]: rexec line 16: Deprecated option UsePrivilegeSeparation
     Sep 12 10:33:47 ck4.kenda.com.tw sshd[8798]: rexec line 19: Deprecated option KeyRegenerationInterval
     Sep 12 10:33:47 ck4.kenda.com.tw sshd[8798]: rexec line 20: Deprecated option ServerKeyBits
     Sep 12 10:33:47 ck4.kenda.com.tw sshd[8798]: rexec line 32: Deprecated option RSAAuthentication
     Sep 12 10:33:47 ck4.kenda.com.tw sshd[8798]: rexec line 39: Deprecated option RhostsRSAAuthentication
     Sep 12 10:33:47 ck4.kenda.com.tw sshd[8798]: reprocess config line 32: Deprecated option RSAAuthentication
     Sep 12 10:33:47 ck4.kenda.com.tw sshd[8798]: reprocess config line 39: Deprecated option RhostsRSAAuthentication
     Sep 12 10:33:47 ck4.kenda.com.tw sshd[8798]: Accepted publickey for root from 192.1.1.115 port 57115 ssh2: RSA SHA256:IyV2HHocY5Ku6F9KY1aVoyG38DkWwcBx9xYxu7Bltq8
     Sep 12 10:33:47 ck4.kenda.com.tw sshd[8798]: pam_unix(sshd:session): session opened for user root by (uid=0)
  ```

- 編輯 server /etc/ssh/sshd_config 設定檔，並下 `systemctl restart sshd` 重啟sshd服務  
  `RSAAuthentication yes`   
  `PubkeyAuthentication yes`   
  `AuthorizedKeysFile      %h/.ssh/authorized_keys`   
- 於 client 產生 SSH KEY `ssh-keygen`  
- 將ssh public key copy到server上 `ssh-copy-id user@server`
- 測試連線 `ssh user@server`

- 連線若仍需要密碼需確認server檔案及目錄權限  `chmod -R 700 ~/.ssh`

