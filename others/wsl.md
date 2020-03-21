## [用WSL 構建Golang Windows和Linux並存開發環境]

- 安裝WSL
	 -  開始 > 設定 >  應用程式與功能 > 程式和功能 > 開啟或關閉Windows功能 > 適用於Linux的Windows子系統 > 勾選確認後重開機。
	 - Microsoft Store 中搜尋 Ubuntu 18.04 > 點擊安裝。(若安裝失敗可以開啟開發者模式再進行安裝)
- 安裝常用套件
	```
	$ sudo apt upgrade && sudo apt update && sudo apt install zsh wget zip unzip git autojump && sudo apt-get clean && sudo apt-get autoclean && sudo apt-get autoremove 
    $ sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
	```   
- 安裝zsh常用套件
	```
	$ git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions   
	$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
	$ git clone git://github.com/wting/autojump.git ~/.oh-my-zsh/custom/plugins/autojump
	$ git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k   
	```
	
- 安裝zsh常用套件(autojump)
	```
	git clone https://github.com/joelthelion/autojump.git
    cd autojump
    ./install.py
	```
	会提示将如下代码安装到.zshrc文件中
	```
	[[ -s ~/.autojump/etc/profile.d/autojump.zsh ]] && . ~/.autojump/etc/profile.d/autojump.zsh
    autoload -U compinit && compinit -u
	```
	
- 修改~/.zshrc裡的plugins  
   ``` 
   plugins=(git zsh-autosuggestions zsh-syntax-highlighting autojump extract)
   ``` 
- 修改~/.zshrc裡的ZSH_THEME
   ```
   ZSH_THEME="powerlevel9k/powerlevel9k"
   ```
- 安裝Powerline字體
   ```
   $ git clone https://github.com/powerline/fonts.git
   $ cd fonts
   $ ./install.sh
   ```
- 安裝Cascadia Code PL字體
  ‵‵‵
	$ wget https://github.com/microsoft/cascadia-code/releases/download/v1911.21/CascadiaPL.ttf -O ~/.local/share/fonts/CascadiaPL.ttf
  ‵‵‵
- VSCode WSL 設定 (setting.json)
  ```json
  {
    "editor.fontFamily": "'Cascadia Code PL','DejaVu Sans Mono for Powerline',Consolas, 'Courier New', monospace",
    "editor.mouseWheelZoom": true,
    "editor.fontLigatures": true
  }
  ```
  
- 安裝WSLtty 
	WSLtty 官網有個 Windows 安裝程式，你直接到 Release 頁面下載最新版安裝並重開機即可。 [請抓*.exe 目前最新版本sltty-1.9.6-install.exe](wsltty-1.9.6-install.exe)
- 設定WSLtty
	設定檔位於%APPDATA\wsltty\config，常用的修改如下：
	```
	Background=/mnt/d/Spotlight/d6e0923c-8f38-4727-99f2-fd104ecce5da.jpg,39  #設定背景(WSL中磁碟都掛在/mnt下, /mnt/C == C:\)
	Transparency=high                                                        #透明度
	Font=Meslo LG L DZ for Powerline                                         #字型    (強烈建議下載並安裝此字型, 使用體驗差很多)
	```
# 以下操作都在WSLtty中操作
- 安裝golang 
	- 下載golang (linux-amd64)  
		下載的地址通常在 https://golang.org/dl/  
		![](https://i.imgur.com/pPfMWbm.png)  
		```
		$ wget https://dl.google.com/go/go1.12.3.linux-amd64.tar.gz
		```
	- 檢查SHA256 Checksum是否與確認與官網上一致是好習慣   
		![](https://i.imgur.com/scbudLn.png) 
		```
		$ sha256sum go1.12.3.linux-amd64.tar.gz
		3924819eed16e55114f02d25d03e77c916ec40b7fd15c8acb5838b63135b03df go1.12.3.linux-amd64.tar.gz
		```
	- 解壓縮 & 放到 /usr/local
		```
		$ tar xvf go1.12.3.linux-amd64.tar.gz
		$ sudo mv go /usr/local
		```
	- 設定環境變數到~/.zshrc (建議跟WINDOWS分開)
		```
		$ vi ~/.zshrc
		```
		加入下列這幾行
		```
		export GOPATH=/mnt/d/go
		export GOROOT=/usr/local/go
		export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
		```
		重啟WSLtty或source ~/.zshrc 即可讓環境邊數生效
		```
		$ source ~/.zshrc
		```
		確認安裝是否成功
		```
		$ go version
		go version go1.11.1 linux/amd64
		```
- 安裝golang常用套件
	- grpc-gateway
		```
		$ go get -u github.com/grpc-ecosystem/grpc-gateway/protoc-gen-grpc-gateway
		$ go get -u github.com/grpc-ecosystem/grpc-gateway/protoc-gen-swagger
		$ go get -u github.com/golang/protobuf/protoc-gen-go
		```
	- 安裝protobuf
		- 下載protobuf套件 (linux-x86_64)
			下載的地址通常在 https://github.com/protocolbuffers/protobuf/releases
			```
			$ wget https://github.com/protocolbuffers/protobuf/releases/download/v3.7.1/protoc-3.7.1-linux-x86_64.zip
			$ sudo unzip protoc-3.7.1-linux-x86_64.zip -d /usr/local/protoc-3.7.1
			```
		- 設定環境變數到~/.zshrc 
			```
			$ vi ~/.zshrc
			```
			加入下列這幾行
			```
			export PATH=$PATH:/usr/local/protoc-3.7.1/bin:/usr/local/protoc-3.7.1/include
			```
			重啟WSLtty或source ~/.zshrc 即可讓環境邊數生效

	- 安裝 go_odbc
		https://gitlab.kenda.com.tw/playground/go_odbc/blob/master/Dockerfile
	- 安裝 go-open62541
		https://gitlab.kenda.com.tw/kenda/plink/blob/master/go-opc-client/Dockerfile
- 設定SSH-KEY
	- 建立SSH-KEY
		```
		$ ssh-keygen -t rsa -b 4096 -f $HOME/.ssh/id_rsa -P ""
		Generating public/private rsa key pair.
        Created directory '/home/saxon/.ssh'.
        Your identification has been saved in /home/saxon/.ssh/id_rsa.
        Your public key has been saved in /home/saxon/.ssh/id_rsa.pub.
        The key fingerprint is:
        SHA256:iJtjxNqxLLDa8EmU8SXGqxxxxRp6yDaVMwpBWE8De4 saxon@DESKTOP-OG40O5V
        The key's randomart image is:
        +---[RSA 4096]----+
        |oo==             |
        |=+ooo            |
        |*.+=+..          |
        |o*.*=+ .         |
        |+ Eo* . S        |
        |o=.= =           |
        |Bo+ O            |
        |o*.+ .           |
        |. +              |
        +----[SHA256]-----+
		$ cat  $HOME/.ssh/id_rsa.pub
		ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDUsR9w4B/MW9mfNvP/lYrbTd908qNoCq9JaosprGiO
		Pc3CneaD/P83XD4BtULP0ntT2ySn14NHH1XCKIa4ChzCVnKAdVAj1uyF262dESiq71Qn7Zv/qS5tjfvD
		gdvtU97D9SgWyB+prodk/+rvuldPuqxxxxxxxjDUvr3nzIskCoqxg0mYNnnrcpyl4mTuHJxLeTSw+wZF
		MT46qHCiB9Bk0qlmaRPoLC8aZtehW5UtkcP4TomM3fgziynS0U5FrKadi9/TpqWN5w3WQVQIgRWtRu06
		qu9vZMBfh7RZmrQAz8DwSmw1OqnGWc4Cc24CjYMkXYiIjUbPzboaNbSDfVXg+55CwSl8gLJLN0DBuWz9
		190WCo50MmwjbDdUz3+386G5fg+6/eBxhbb+4ZAaZQFP1MPcofEQ8AUnI5SLKokoWHczfRcwSbkydREs
		2hRebuU7FcpJWpGWXCiDrgq0UYPjTJum88E+VeQqrzIxm6cQ2+BE9KZbJhFmAyvigZjCjZSNkSYdh1eH
		EbgLTKkEyldK9KP6bKEOvWsG1exxxxxxxxxxx7DI6u87ekusB05wZmjUXE2gLxRlDLUcNa7iF01FQfRT
		oldNTw82p3D5NuDdLJxo/g/0XZBSEV86KdRVyADCi+4VqFj9w3v0JCgCp9Dt1JYD7PNXAlwgNyFlp1CE
		Ow== saxon@DESKTOP-OG40O5V
		```
	- 將id_rsa.pub貼到gitlab SSH Keys設定中
- 讓執行 sudo 的時候免輸入密碼  (請記得將 saxon 換成你自己的 WSL 登入帳號。) 
 	```
	$ sudo echo "saxon ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/saxon
	```
- 安裝 Docker CE for Ubuntu
	```
	# Update the apt package index:
	$ sudo apt-get update 

	# Install packages to allow apt to use a repository over HTTPS:
	$ sudo apt-get install \
	    apt-transport-https \
	    ca-certificates \
	    curl \
	    software-properties-common

	# Add Docker's official GPG key
	$curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

	# Verify that you now have the key with the fingerprint 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88, by searching for the last 	8 characters of the fingerprint.
	$ sudo apt-key fingerprint 0EBFCD88

	# Use the following command to set up the "edge" repository
	# 目前 (2018/6/15) 在 Ubuntu 18.04 僅支援 Edge Channel https://github.com/docker/for-linux/issues/290
	$ sudo add-apt-repository \
	   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
	   $(lsb_release -cs) \
	   edge"

	# Install Docker CE

	$ sudo apt-get update
	$ sudo apt-get install docker-ce -y

	# Connect to local DOCKER_HOST ("Docker for Windows")
	# remember to enable "Expose daemon on tcp://localhost:2375 without TLS
	$ docker -H localhost:2375 version

	$ export DOCKER_HOST=localhost:2375

	$ docker version
	$ echo "export DOCKER_HOST=localhost:2375" >> ~/.zshrc
	```
- 設定git  
	- 常用的git alias
		```
		$ git config --global alias.co   checkout
		$ git config --global alias.ci   commit
		$ git config --global alias.st   status
		$ git config --global alias.sts  "status -s"
		$ git config --global alias.br   branch
		$ git config --global alias.re   remote
		$ git config --global alias.di   diff
		$ git config --global alias.type "cat-file -t"
		$ git config --global alias.dump "cat-file -p"
		$ git config --global alias.lo   "log --oneline"
		$ git config --global alias.ll   "log --pretty=format:'%h %ad | %s%d [%Cgreen%an%Creset]' --graph --date=short"
		$ git config --global alias.lg   "log --graph --pretty=format:'%Cred%h%Creset %ad |%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset	[%Cgreen%an%Creset]' --abbrev-commit --date=short"
		```
	- 設定 Git 基本 user.name 與 user.email 參數
		```
		git config --global user.name "saxon[莊聖瑋]"
		git config --global user.email "saxon@kenda.com.tw"
		```
- 解決在 WSL 在 Disk I/O 超級超級慢的問題 (關閉 Windows Defender 即時保護功能)

	以下是暫時關閉「即時保護」功能，請以系統管理員身分開啟 PowerShell 並執行以下命令：
	```
	$ Set-MpPreference -DisableRealtimeMonitoring $true
	```
	如果要在 Command Prompt (命令提示字元) 下執行，可以參考以下命令：
	```
	$ powershell -Command "Set-MpPreference -DisableRealtimeMonitoring $true"
	```
	如果要快速切換回安全設定，可以參考以下命令：
	```
	$ PowerShell: Set-MpPreference -DisableRealtimeMonitoring $false
	```
	如果要在 Command Prompt (命令提示字元) 下執行，可以參考以下命令： 
	```
	$ powershell -Command "Set-MpPreference -DisableRealtimeMonitoring $false"
	```
- 參考資料   
	[我的 Windows Subsystem for Linux (WSL) 終極開發人員配置 - 2018 版](https://blog.miniasp.com/post/2018/06/15/My-Windows-Subsystem-for-Linux-WSL-Setup-2018)   
	[淺嚐Windows Subsystem for Linux](https://crzidea.com/2016/11/30/start-using-windows-subsystem-for-linux/)   
	[用Oh My Zsh把iTerm變美美](https://medium.com/@hazelwu/%E7%94%A8oh-my-zsh%E6%8A%8Aiterm%E8%AE%8A%E7%BE%8E%E7%BE%8E-8a18daa8eac)   
	[Ubuntu 下 Oh My Zsh 的最佳实践「安装及配置」](https://segmentfault.com/a/1190000015283092)  