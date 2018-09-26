## Настроим прокси в Ubuntu

> [Крутой гайд](https://help.ubuntu.ru/wiki/%D0%BF%D1%80%D0%BE%D0%BA%D1%81%D0%B8)

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
