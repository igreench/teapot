## Тадаам

```
npm config set proxy http://proxy.company.com:8080
npm config set https-proxy http://proxy.company.com:8080
```
Важный момент, что в **https-proxy** пишется **http** (wtf?!). Такие дела.

***

В редких случаях надо зайти в конфиг
```
sudo mcedit ~/.npmrc
```
И я явно прописать следующее:
```
proxy=http://username:password@proxy.address:port/
https-proxy=http://username:password@proxy.address:port/
strict-ssl=false
registry=http://registry.npmjs.org/
```
