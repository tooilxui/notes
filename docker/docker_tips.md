# show full command of running container

```console
root@/:~$ docker inspect -f "{{.Name}} {{.Config.Cmd}}" $(docker ps -a -q)
```

# show logs of container

```console
root@/:~$ docker logs [OPTIONS] CONTAINER

OPTIONS:
    -f : 跟踪日誌輸出 
    --since :顯示某個開始時間的所有日誌 
    -t : 顯示時間戳 
    --tail :僅列出最新N條容器日誌
```
### 查看plink容器2016-07-01之後的最新10個log
```console
root@/:~$ docker logs --since="2016-07-01" --tail=10 plink
```