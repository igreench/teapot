## Как прокинуть прокси в git?

```
git config --global http.proxy https://[corporate-proxy]
git config --global http.sslVerify false
```

Потребность в установке прокси в git возникла, когда пытался развернуть шаблон через vue-cli, а он использует git.
