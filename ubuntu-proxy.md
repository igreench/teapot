## Настроим прокси в Ubuntu

Откроем конфиг
```
sudo mcedit /etc/environment
```
И впишем следующее:
```
https_proxy="https://user:pass@proxy:port/" 
http_proxy="http://user:pass@proxy:port/"
ftp_proxy="ftp://user:pass@proxy:port/"
socks_proxy="socks://user:pass@proxy:port/"
```
***
В некоторых кейсах в переменную **https_proxy** пишется **http://...**
