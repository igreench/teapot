Тут пробуем прокинуть проксю в докер
## Вариант 1
>Я попробовал первый вариант

Открываем конфиг
```
sudo mcedit ~/.docker/config.json
```
Пишем следующие настройки
```
{
 "proxies":
 {
   "default":
   {
     "httpProxy": "http://proxy.example.com:80",
     "noProxy": "*.test.example.com,.example2.com"
   }
 }
}
```
Перезапускаем докер. Иии... У меня ничего не произошло.
***
## Вариант 2
>[Ссылка на гайд](https://stackoverflow.com/questions/24151129/network-calls-fail-during-image-build-on-corporate-network)

Посмотрим имя соединения
```
ifconfig
```
У меня: **eth0**
Ищем его IP
```
nmcli device show eth0 | grep IP4.DNS
```
У меня: **DNS:192.168.10.100**
Открываем настройки сервиса.

В Ubuntu:
```
sudo mcedit /etc/default/docker
```
В CentOS:
```
sudo mcedit /etc/sysconfig/docker
```
Пишем:
```
DOCKER_OPTS="--dns 8.8.8.8 --dns 8.8.4.4 --dns 192.168.10.100"
```
Перезапускаем докер.
```
sudo service docker restart
```
Или так.
```
sudo systemctl reload docker
```
И опять ничего...
***
## Вариант 3

Мы знаем IP соединения. Открываем файл:
```
sudo mcedit /lib/systemd/system/docker.service
```
Добавляем dns, чтобы выглядело примерно так:
```
ExecStart=/usr/bin/dockerd --dns 8.8.8.8 --dns 8.8.4.4 --dns 192.168.10.100 -H fd://
```
Перезапускаем так:
```
sudo service docker stop
sudo systemctl daemon-reload
sudo service docker start
```
Проверяем:
```
sudo systemctl status docker
```
Тестим.
Иии... Убеждаемся, что всё работает. Ура :)
