## Установим докер на Ubuntu 16.04
> Вроде просто всё, да есть нюансы
Грохнем старый докер. Это надо сделать правильно (а не как я первый раз)
```
sudo apt-get remove docker docker-engine docker.io
sudo apt-get update
```
В теории можно дико накосопорить с контейнерами. Если руки из жопы, как у меня, то можно грохнуть образы и контейнеры:
```
# Delete all containers
docker rm $(docker ps -a -q)
# Delete all images
docker rmi $(docker images -q)
```
Теперь установим докер (паста с оф сайта)
```
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce
```
Проверим, что получилось
```
apt-cache madison docker-ce
sudo docker run hello-world
```
Получилось? Отлично!
А теперь го поставим композер.
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo curl -L https://raw.githubusercontent.com/docker/compose/1.22.0/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
```
Чекнем:
```
docker-compose --version
```
Должно быть всё ок.

И есть один нюанс с правами. Расшарь права юзеру **докер**
```
sudo usermod -a -G docker $USER
```
А теперь немного магии: **разлогинься-залогинься**. Да, это поможет.
