/*
Title: 修改 SSL 证书
Sort: 4
*/
如果最开始部署服务时，未使用 wss ，之后想再添加或者修改 wss 可以用下述方式：

**免费版许可证 ：**

1. 准备好域名对应的ssl证书文件（nginx使用）
2. 进入目录 **/usr/local/nginx/conf/ssl/**
3. 覆盖 **nginx.crt与nginx.key** 文件， **注意：区分两个是不同的证书文件，覆盖文件就好，不要修改文件名。**
4. 执行 **/usr/local/nginx/sbin/nginx -s reload** 命令来重新加载ssl证书



**500 CCU 及以上的许可证：**

1. 准备好wss域名对应的ssl证书（nginx使用）
2. 删除k8s里面默认生成的ssl证书
```shell
kubectl -n matchvs-nginx delete secret nginx-secret
```
3. 将证书复制到一个目录里，然后执行创建secret
```shell
kubectl create secret generic nginx-secret --from-file=./nginx.crt --from-file=./nginx.key -n matchvs-nginx
```
4. 查看当前nginx的Podname
```
[root@8-50 ~]# kubectl -n matchvs-nginx get pods
NAME                           READY     STATUS    RESTARTS   AGE
deploy-nginx-ddc484b46-mlbjx   1/1       Running   5          10d
```
5. 删除Pod，让k8s重新启动nginx来实现加载证书，完成！！
```
kubectl -n matchvs-nginx delete pod deploy-nginx-ddc484b46-mlbjx
```