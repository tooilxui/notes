# 免sudo使用docker命令
## 背景

因為使用的是sudo安裝docker，所以會導致一個問題。以普通用戶登錄的狀況下，在使用docker images時必須添加```sudo```，那麼如何讓docker免```sudo```依然可用呢？於是開始搜索解決方案。
## 理清楚問題
當以普通用戶身份去使用```docker images```時，出現以下錯誤：

    Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.26/images/json: dial unix /var/run/docker.sock: connect: permission denied

可以看到，最後告知我們時權限的問題。那麼在linux文件權限有三個數據左右```drwxrwxrwx```，

![image] (https://gitlab.kenda.com.tw/saxon/notes/blob/master/ubuntu/png/1.png)

其中第一為```d```代表該文件是一個文件夾
前三位、中三位、後三位分別代表這屬主權限、屬組權限、其他人權限。
如圖，其中 第三列、第四列分別代表文件的屬主、屬組。

![image] (https://gitlab.kenda.com.tw/saxon/notes/blob/master/ubuntu/png/2.png)

上圖是報錯文件的權限展示，可以看到其屬主為```root```，權限為```rw```，可讀可寫；其屬組為```docker```，權限為```rw```，可讀可寫。如果要當前用戶可直接讀取該文件，那麼我們就為當前用戶添加到```docker```屬組即可。

## 解決方案
如果還沒有 docker group 就添加一個：

    sudo groupadd docker

將用戶加入該 group 內。然後退出並重新登錄就生效啦。

    sudo gpasswd -a ${USER} docker

重啟 docker 服務

    sudo service docker restart

切換當前會話到新 group 或者重啟 Xterm
    newgrp - docker


>注意:最後一步是必須的，否則因為 groups 命令獲取到的是緩存的組信息，剛添加的組信息未能生效，所以 docker images 執行時同樣有錯。