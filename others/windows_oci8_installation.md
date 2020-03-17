# Oracle Client For Golang On Windows
### This is a step-by-step procedure to get pkg-config working on Windows
### reference from ~~[程子清博客](]http://chengziqing.cn/?p=994)~~ and based on my experience.

### Install mingw
1. go to https://sourceforge.net/projects/mingw-w64/
2. download the installer of mingw-w64 - mingw-w64-install.exe
3. install it at C:\MinGw  (change "Architecture" to "x86_64" if you are usging x64 OS)  
![](https://i.imgur.com/d3mbsEv.png)
4. add "C:\MinGW\bin" to "PATH" environment variable

### Install Oracle Instant Client
1. go to [Oracle Instant Client Downloads](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)
2. download  instantclient-basiclite,basic,sdk   
![](https://i.imgur.com/CyaqE0f.png)
3. extract all three files to C:\instantclient_18_5 (in case the version is 18.5) 
4. add "C:\instantclient_18_5" to "PATH" environment variable

### Install oci8.pc
1. copy oci8.pc to "C:\MinGW\lib\pkg-config"
2. modify oci8.pc and set right Oracle CLient "Version Info" and "Directory Path". 注意斜線方向(/)  
    ```sh
    prefix=C:/instantclient_19_5
    exec_prefix=${prefix}
    libdir=${prefix}/sdk/lib/msvc
    includedir=${prefix}/sdk/include
    
    glib_genmarshal=glib-genmarshal
    gobject_query=gobject-query
    glib_mkenums=glib-mkenums
    
    Name: oci8
    Description: oci8 library
    Libs: -L${libdir} -loci
    Cflags: -I${includedir}
    Version: 19.5
    ```
3. add "C:\MinGW\lib\pkg-config" to "PKG_CONFIG_PATH" environment variable

### Install pkg-config
1. go to http://ftp.gnome.org/pub/gnome/binaries/win32/dependencies/
  - download the file [pkg-config_0.26-1_win32.zip](http://ftp.gnome.org/pub/gnome/binaries/win32/dependencies/pkg-config_0.26-1_win32.zip)
  - extract the file bin/pkg-config.exe to C:\MinGW\bin
  - download the file [gettext-runtime_0.18.1.1-2_win32.zip](http://ftp.gnome.org/pub/gnome/binaries/win32/dependencies/gettext-runtime_0.18.1.1-2_win32.zip)
  - extract the file bin/intl.dll to C:\MinGW\bin
2. go to http://ftp.gnome.org/pub/gnome/binaries/win32/glib/2.28
  - download the file [glib_2.28.8-1_win32.zip](http://ftp.acc.umu.se/pub/gnome/binaries/win32/glib/2.28/glib_2.28.8-1_win32.zip)
  - extract the file bin/libglib-2.0-0.dll to C:\MinGW\bin
3. check pkg-config list by cmd.exe
   ```
   C:\Users\saxon.KENDA>pkg-config.exe --version
   0.23
   C:\Users\saxon.KENDA>pkg-config.exe --cflags oci8
   -IC:/instantclient_18_5/sdk/include/
   C:\Users\saxon.KENDA>pkg-config.exe --list-all
   oci8 OCI - Oracle database engine
   ( ↑ result will content oci8, but this command doesn't will on git-bash, please use cmd.) 
   ```
   
### Install C/C++ compiler (maybe just need one of them)
1. Microsoft Visual C++ 2012 update4.exe 
2. Microsoft Visual C++ 2013.exe
3. Microsoft Visual C++ 2015 X64.exe
4. Microsoft Visual C++ 2017-2019 X64.exe
    