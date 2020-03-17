# Display Link Setup

## Before start you should know :
   - this paper has tested in:
        - Laptop : Acer Swift-SF713-51
        - OS : ubuntu 19.10 
        - Display Link Device: dell D3100
   
## Setps :
   - install `displaylink-debain` and `reboot`
   
        git clone https://github.com/AdnanHodzic/displaylink-debian.git
        cd displaylink-debian/ && sudo ./displaylink-debian.sh

   - rename `20-displaylink.conf` and `reboot` (delete the conf file works too.)
   
        sudo mv /etc/X11/xorg.conf.d/20-displaylink.conf /etc/X11/xorg.conf.d/20-displaylink.conf.bk
    
   - everything is ok.
   
## Reference links : 
   - [Displaylink-debain](https://github.com/AdnanHodzic/displaylink-debian)
   - [Displaylink-debain Post Installation Guide](https://github.com/AdnanHodzic/displaylink-debian/blob/master/post-install-guide.md)
   - [can't work after instal displaylink](https://github.com/AdnanHodzic/displaylink-debian/issues/228#issuecomment-467979471)
   