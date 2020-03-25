# [用WSL 構建Golang Windows和Linux並存開發環境]

- 安裝WSL
  - 開始 > 設定 >  應用程式與功能 > 程式和功能 > 開啟或關閉Windows功能 > 適用於Linux的Windows子系統 > 勾選確認後重開機。
  - Microsoft Store 中搜尋 Ubuntu 18.04 > 點擊安裝。(若安裝失敗可以開啟開發者模式再進行安裝)
- 安裝常用套件
  ```sh
  sudo apt upgrade && sudo apt update && \
  sudo apt install zsh wget zip unzip git autojump && \
  sudo apt-get clean && sudo apt-get autoclean && sudo apt-get autoremove  
  sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
  ```
- 安裝zsh常用套件
  ```sh
  # autosuggestions
  git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
  
  # syntax-highlighting
  git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
  
  # zsh theme: powerlevel9k
  git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k

  # autojump
  git clone git://github.com/wting/autojump.git ~/.oh-my-zsh/custom/plugins/autojump  
  cd ~/.oh-my-zsh/custom/plugins/autojump
  ./install.py

  # 會提示將如下指令cop到~/.zshrc中
  echo '[[ -s ~/.autojump/etc/profile.d/autojump.zsh ]] && . ~/.autojump/etc/profile.d/autojump.zsh
   autoload -U compinit && compinit -u'  > ~/.zshrc

  # 修改~/.zshrc裡的plugins
  plugins=(git zsh-autosuggestions zsh-syntax-highlighting autojump extract)
  
  # 修改~/.zshrc裡的ZSH_THEME
  ZSH_THEME="powerlevel9k/powerlevel9k"
  # 安裝Powerline字體
  git clone https://github.com/powerline/fonts.git
  cd fonts
  ./install.sh
  # CascadiaPL
  wget https://github.com/microsoft/cascadia-code/releases/download/v1911.21/CascadiaPL.ttf -O ~/.local/share/fonts/CascadiaPL.ttf
  # DejaVuSansMono
  wget github.com/powerline/fonts/blob/master/DejaVuSansMono/DejaVu%20Sans%20Mono%20for%20Powerline.ttf -O ~/.local/share/fonts/DejaVu%20Sans%20Mono%20for%20Powerline.ttf
  ```
- 安裝golang
    ```sh
    # this example is version 1.12.3, remember change the version you want
    # the newest version please follow this link: https://golang.org/dl/
    wget https://dl.google.com/go/go1.12.3.linux-amd64.tar.gz

    # extract and put into /usr/local
    tar xvf go1.12.3.linux-amd64.tar.gz
    sudo mv go /usr/local

    # set GOPATH & GOROOT,  (GOROOT建議跟WINDOWS分開, GOPATH可共用)
    echo 'export GOPATH=/mnt/d/go' > ~/.zshrc
    echo 'export GOROOT=/usr/local/go' > ~/.zshrc
    echo 'export PATH=$PATH:$GOROOT/bin:$GOPATH/bin' > ~/.zshrc

    # installation testing
    go version # it will show golang version on terminal
    ```
- 安裝golang常用套件
    ```sh
    # grpc-gateway
    go get -u github.com/grpc-ecosystem/grpc-gateway/protoc-gen-grpc-gateway
    go get -u github.com/grpc-ecosystem/grpc-gateway/protoc-gen-swagger
    go get -u github.com/golang/protobuf/protoc-gen-go

    # protobuf (linux-x86_64)
    # this example is version 3.7.1, remember change the version you want
    # the newest version please follow this link: https://github.com/protocolbuffers/protobuf/releases
    wget https://github.com/protocolbuffers/protobuf/releases/download/v3.7.1/protoc-3.7.1-linux-x86_64.zip
    sudo unzip protoc-3.7.1-linux-x86_64.zip -d /usr/local/protoc-3.7.1

    # set protobuf into $PATH (reopen terminal or `source ~/.zshrc` could active environment variable)
    echo 'export PATH=$PATH:/usr/local/protoc-3.7.1/bin:/usr/local/protoc-3.7.1/include' > ~/.zshrc
    ```
  - go_odbc (installation steps, please follow this [link](https://gitlab.kenda.com.tw/playground/go_odbc/blob/master/Dockerfile))
  - go-open62541 (installation steps, please follow this [link](https://gitlab.kenda.com.tw/kenda/plink/blob/master/go-opc-client/Dockerfile))

- 設定SSH-KEY
  - 建立SSH-KEY (記得將 ~/.ssh/id_rsa.pub 的內容貼到gitlab SSH Keys設定中)
    ```sh
    ssh-keygen -t rsa -b 4096 -f $HOME/.ssh/id_rsa -P ""
    ```
- 讓執行 sudo 的時候免輸入密碼  (請記得將 saxon 換成你自己的 WSL 登入帳號。)
   ```sh
  sudo echo "saxon ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/saxon
  ```  
- 安裝 Docker CE for Ubuntu  
  ```sh
  # Update the apt package index:
  sudo apt-get update

  # Install packages to allow apt to use a repository over HTTPS:
  sudo apt-get install \
      apt-transport-https \
      ca-certificates \
      curl \
      software-properties-common

  # Add Docker's official GPG key
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

  # Verify that you now have the key with the fingerprint 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88, by searching for the last   8 characters of the fingerprint.
  sudo apt-key fingerprint 0EBFCD88

  # Use the following command to set up the "edge" repository
  # 目前 (2018/6/15) 在 Ubuntu 18.04 僅支援 Edge Channel https://github.com/docker/for-linux/issues/290
  sudo add-apt-repository \
     "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) \
     edge"

  # Install Docker CE
  sudo apt-get update
  sudo apt-get install docker-ce -y
  
  # Connect to local DOCKER_HOST ("Docker for Windows")
  docker -H localhost:2375 version

  # remember to enable "Expose daemon on tcp://localhost:2375 without TLS
  $ echo "export DOCKER_HOST=localhost:2375" >> ~/.zshrc
  ```
- 設定git  
  - 常用的git alias
    ```sh
    git config --global alias.co   checkout
    git config --global alias.ci   commit
    git config --global alias.st   status
    git config --global alias.sts  "status -s"
    git config --global alias.br   branch
    git config --global alias.re   remote
    git config --global alias.di   diff
    git config --global alias.type "cat-file -t"
    git config --global alias.dump "cat-file -p"
    git config --global alias.lo   "log --oneline"
    git config --global alias.ll   "log --pretty=format:'%h %ad | %s%d [%Cgreen%an%Creset]' --graph --date=short"
    git config --global alias.lg   "log --graph --pretty=format:'%Cred%h%Creset %ad |%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset  [%Cgreen%an%Creset]' --abbrev-commit --date=short"
    ```
  - 設定 Git 基本 user.name 與 user.email 參數
    ```sh
    git config --global user.name "saxon[莊聖瑋]"
    git config --global user.email "saxon@kenda.com.tw"
    ```
	
- VScode字型設定 (Cascadia字型要先安裝在windows上)
  - settings.json
  ```json
  {
      "go.useLanguageServer": true,
      "window.zoomLevel": 0,
      "terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe",
      "terminal.integrated.fontFamily": "\"Cascadia Code PL\"",
      "editor.fontFamily": "'Cascadia Code PL',Consolas, 'Courier New', monospace",
      "editor.mouseWheelZoom": true
  }
  ```
- Windows Terminal Preview 字型設定 (Cascadia字型要先安裝在windows上)
  - profile.json
  ```json
    "profiles":
    {
        "defaults":
        {
            "fontFace":  "Cascadia Code PL"
        },
		/// .... 以下省略
	}
  ```

- 解決在 WSL 在 Disk I/O 超級超級慢的問題 (關閉 Windows Defender 即時保護功能)
  以下是暫時關閉「即時保護」功能，請以系統管理員身分開啟 PowerShell 並執行以下命令：
  ```sh
  Set-MpPreference -DisableRealtimeMonitoring $true
  ```
  如果要在 Command Prompt (命令提示字元) 下執行，可以參考以下命令：
  ```sh
  powershell -Command "Set-MpPreference -DisableRealtimeMonitoring $true"
  ```
  如果要快速切換回安全設定，可以參考以下命令：
  ```sh
  PowerShell: Set-MpPreference -DisableRealtimeMonitoring $false
  ```
  如果要在 Command Prompt (命令提示字元) 下執行，可以參考以下命令：
  ```sh
  powershell -Command "Set-MpPreference -DisableRealtimeMonitoring $false"
  ```
- 參考資料
  [我的 Windows Subsystem for Linux (WSL) 終極開發人員配置 - 2018 版](https://blog.miniasp.com/post/2018/06/15/My-Windows-Subsystem-for-Linux-WSL-Setup-2018)  
  [淺嚐Windows Subsystem for Linux](https://crzidea.com/2016/11/30/start-using-windows-subsystem-for-linux/)  
  [用Oh My Zsh把iTerm變美美](https://medium.com/@hazelwu/%E7%94%A8oh-my-zsh%E6%8A%8Aiterm%E8%AE%8A%E7%BE%8E%E7%BE%8E-8a18daa8eac) 
  [Ubuntu 下 Oh My Zsh 的最佳实践「安装及配置」](https://segmentfault.com/a/1190000015283092)
