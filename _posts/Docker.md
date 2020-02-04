# Increase Docker Download Speed in China Mainland #

Using Azure mirror and Netease mirror.

Add these in `/etc/docker/daemon`. (touch this file if it does not exist)

```
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://hub-mirror.c.163.com"
  ]
}
```
Then run

``` shell
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

