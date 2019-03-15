/*
Title: 防火墙配置说明
Sort: 7
*/

引擎服务通过kubernetes 集群来提供服务，请确保kubernetes之间的node可以通过内网互通(内网请勿配置防火墙，会导致引擎或者kubernetes 之间访问出现异常)。
查看引擎开放了那些端口，请登录任意一台服务器，执行命令：
`kubectl -n matchvs-engine get svc -o wide` 其中 PORTS列表即为引擎所需要开放的服务端口
引擎开放端口示例：

```shell
NAME               TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)                       AGE       SELECTOR
svc-directory      NodePort   10.254.154.138   <none>        9981:9981/TCP,9982:9982/TCP   27d       SERVER=DIRECTORY,TYPE=MATCHVS
svc-gateway        NodePort   10.254.255.110   <none>        7001:7001/TCP                 27d       SERVER=GATEWAY,TYPE=MATCHVS
svc-hotel-9330     NodePort   10.254.244.133   <none>        9330:9330/TCP                 27d       PORT=PORT_9330,SERVER=HOTEL,TYPE=MATCHVS
svc-matchvsadmin   NodePort   10.254.50.57     <none>        8081:8081/TCP                 27d       SERVER=matchvsadmin,TYPE=MATCHVS
svc-statistics     NodePort   10.254.147.244   <none>        7749:7749/TCP                 27d       SERVER=STATISTICS,TYPE=MATCHVS
```

其中确保配置外网能够访问 `gateway(7001) hotel(933*) nginx(80,443)` 这几个外部端口，其余只需内网服务可以访问到即可。