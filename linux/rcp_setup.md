rcp用途：在本地主机和远程主机之间或者两个远程主机之间传输文件。详细用法可man rcp查看。

现在要把主机10.200.5.200的/tmp/work.sh（属主为root用户）拷贝到远程主机180.200.3.100的/tmp，配置过程如下：

## 1、在两台主机的/etc/hosts文件添加对方ip<--->hostname映射

    在10.200.5.200的/etc/hosts添加一行内容：180.200.3.100 AIX432

    在180.200.3.100的/etc/hosts添加一行内容：10.200.5.200 AIX53

## 2、在两台主机的root用户家目录中的.rhosts文件中添加以下内容：

    10.200.5.200的root $HOME/.rhosts中添加：AIX432 root

    180.200.3.100的root $HOME/.rohosts中添加：AIX53 root

## 3、通过rcp执行远程复制

    在10.200.5.200主机以root用户执行以下命令：

    AIX53# rcp /tmp/work.sh AIX432:/tmp   #此时要确保AIX432主机/tmp目录下没有work.sh文件，如果有，将被自动覆盖。

    如果要把10.200.5.200上的一个目录（包括子目录及其文件）复制到远程主机，可通过rcp命令参数：-r

## 关于/etc/hosts.equiv和$HOME/.rhosts文件的区别如下：

首先这两个文件都是和主机间的信任关系相关的，也就是允许另外一台机器上的用户不用输密码就可以rlogin到本机。但是，这两个文件之间的区别在哪里呢？
总结一下：
+ 1.首先这两个文件里面的有效格式是一样的，也就是都可以是下面的格式

    `主机名`

    `主机名 用户名`

+ 2.首先明确如果有远程主机想rlogin到本机，本机都要做什么

    1. rlogind检查本机/etc/passwd中是否有远程的用户名，没有则拒绝访问
    2. 如果本机/etc/passwd中有远程的用户名，并且该用户名不是root，则先检查/etc/hosts.equiv，看看里面是否存在远程主机名，如果存在，则允许访问。
    如果是root，则跳过对etc/hosts.equiv的检查，只检查“～home/.rhosts”。
    3. /etc/hosts.equiv中只有远程主机名的话，表示允许远程主机上的所有非root用户可以登录本地主机，并且不需要密码
    /etc/hosts.equiv 文件中如果加入了一台远程主机和一个用户，在远程主机上的那个用户（非root用户）就可以用rlogin登录本地机上的所有非root账号，而且不需要密码。
    4. $HOME/.rhosts文件中如果是远程主机名的话，则只信任由远程主机上与.rhosts属主同名的用户。
    5. $HOME/.rhosts文件中如果是远程主机名+用户名的话，则远程主机上对应的那些用户都可以登录过来到本地主机，但是在本地主机上显示的用户权限是对应$HOME的那个本地用户

## rsh, rcp, rlogin, rdist 的配置

    本文来自：http://www-01.ibm.com/support/docview.wss?uid=csc149f4fd6617be81a548256f78002768da

    在使用rsh, rcp, rlogin, rdist 等各种远程命令时有时会出现许可被拒（Permission Denied ）等错误，如下所示：
    rcp 0826-813 – permission denied
    本文档将针对于如何调试解决这些问题进行简单的讨论，并通过实例说明几种常用的调试方法及其过程。内容适用于AIX 5L 以及AIX 4.3。
### 说明:
+ 检查这些远程命令所需要的基本设置
配置这些远程命令需要正确设置 .rhosts 或者 hosts.equiv 文件。两个关键点是:
    1. 确认相关文件的内容以及权限设置
    .rhosts 和hosts.equiv分别位于$HOME目录以及/etc目录下。它们的许可权限应该设置为600。内容应该包括你正在使用的主机的机器名以及你登录的 用户名。特别注意文件中登记的主机名要和实际的主机名完全匹配。这些文件应该存在于远程命令操作的服务器上。参看man 帮助可以得到更多的关于这两个文件及其要求的格式的相关信息。
    2. 确认正向及反向名解析（ forward and reserve name resolution ）在两个系统上能正常工作，并且解析的结果和你想要的一致。检测正向及反向名解析（ forward and reserve name resolution ）可以使用host 命令检查主机名和IP地址。参见下列例子：
    在本例中你想 rsh 从一台叫 fozzie 主机到一台名叫 bert 的机器，并且作为root运行date命令。

    在fozzie 机器上：

    `rsh bert date`
    `#rshd: 0826-813 Permission is denied`

    出现许可被拒的错误，现在到bert机器上检查文件内容以及权限设置：

    `# cat /.rhosts |grep fozzie`
    `#fozzie root`

    （正确的 .rhosts文件中存在客户机的主机名和要登录进来的用户名)

    `# ls -l /.rhosts`
    `# -rw-------` （许可权限设置为600，也是正确！)

    文件内容和权限设置似乎都正确，那么问题出在什么地方呢？再来检查名解析:

    `# cat /etc/netsvc.conf`
    `# hosts=local,bind4`
    （显示名解析将先使用本机/etc/hosts，然后才是 DNS）
    
    `# cat /etc/hosts |grep fozzie`
    `#`

    (在本机host文件中没有发现fozzie，因此bert 解析 fozzie 经DNS)    

    `# host fozzie`
    `#fozzie.austin.ibm.com is 10.1.4.18`
    `# host 10.1.4.18`
    `#fozzie.austin.ibm.com is 10.1.4.18`

    由此可知，通过DNS解析出的fozzie实际上是fozzie.austin.ibm.com，这和包含在.rhosts 中的fozzie是不匹配的。为了解决这个问题你可以通过编辑/etc/hosts增加一个fozzie的短名字，或者编辑.rhosts使fozzie 使用其长名。为了和/etc/netsvc.conf保持一致，编辑/etc/hosts文件

    `# vi /etc/hosts`
    
    增加以下一行
    
    `10.1.4.18 fozzie`

    再次使用host命令，

    `# host fozzie`

    `fozzie is 10.1.4.18`

    `# host 10.1.4.18`

    `fozzie is 10.1.4.18`

    现在回到fozzie 机器，再试rsh

    `# rsh bert date`

    `#Tue May 21 10:16:39 CDT 2004`
+ 利用 who am i 命令进行调试
    另一个在调试远程命令的名解析功能时很有用的命令是who am i。参见下例：
    例：

        telnet 或者 rlogin 从fozzie 到 bert 然后运行 who am i

        `#who am i`

        `#root pts/0 Sep 21 10:18 (10.1.4.18)`

        这显示命令执行者（我-i）是root ，IP地址为10.1.4.18。bert 是通过IP 地址而不是通过名字来找fozzie。为了运行远程命令，在bert上的
        .rhosts中必须反映这一点。所以.rhosts文件应该包含：
        
        `10.1.4.18 root`

+ 其它调试技巧
    在调试远程命令相关的问题中，其它的相关检查项目可能包括：
    1. 身份验证（ authentication）：

        `# lsauthent-->krb5 standard AIX`
        
        可能需要更改为 std
        
        `# chauthent -std`
        
        `# lsauthent-> Standard AIX`
    
    2. 各种命令的许可权限（ permissions ）

        `# ls -al /usr/bin/rsh`

        `-r-sr-xr-x 2 root system 303506 Feb 10 14:11 /usr/bin/rsh`
        
        `# ls -l /usr/sbin/rshd`
        
        `-r-sr-xr-- 1 root system 24556 Feb 10 14:12 /usr/sbin/rshd`
        
        `# ls -l /usr/bin/rcp`
        
        `-r-sr-xr-x 1 root system 319972 Apr 08 2001 /usr/bin/rcp`
        
        `# ls -al /usr/bin/rlogin`
        
        `-r-sr-xr-x 1 root bin 306328 Apr 10 2002 /usr/bin/rlogin`
        
        `# ls -al /usr/sbin/rlogind`
        
        `-r-sr-xr-- 1 root system 33864 Jul 17 2002/usr/sbin/rlogind`
        
        除了rshd 和rlogind 是 4554外 ，其它各项都应该是4555 -r-sr-xr-x 。