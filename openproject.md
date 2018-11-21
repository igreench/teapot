# Ставим и запускаем Open Project из докера

```bash
sudo mkdir -p /Users/evgeny/temp/openproject/{pgdata,logs,static}

docker run -d -p 8090:80 --name openproject -e SECRET_KEY_BASE=secret \
  -v /temp/openproject/pgdata:/var/lib/postgresql/9.6/main \
  -v /temp/openproject/logs:/var/log/supervisor \
  -v /temp/openproject/static:/var/db/openproject \
  openproject/community:8
```

Стопнем его

```bash
docker stop openproject
```

И опять запустим

```bash
docker start openproject
```
