# vwifi OpenWRT package (Raizo62/vwifi)

This package provides a client for the vwifi virtual WiFi system, following the
setup instructions for OpenWRT x86_64.
The makefile is self contained, it uses packages from OpenWRT and applies the
patches suggested in the official readme for OpenWRT. 

## Build your image with this package 

To use it just:

1. Clone OpenWRT 
2. add the repo to the feeds.conf file
3. run scripts/feeds update.a 
4. run scripts/feeds install.a 
5. run make menuconfig to enable vwifi package 
6. Build your image  

## Configure
in the host before starting the service 
```bash
uci set vwifi.config.server_ip=192.168.126.187
uci set vwifi.config.mac_prefix="74:f8:f6:66"
uci set vwifi.config.enabled='1'
uci commit vwifi
```
```
service vwifi-client start
``

## References

- [vwifi GitHub Repository](https://github.com/Raizo62/vwifi)
- [Install vwifi on OpenWRT X86_64 documentation](https://github.com/Raizo62/vwifi/wiki/Install-on-OpenWRT-X86_64).
