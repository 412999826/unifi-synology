# 群晖NAS安装UniFi Controller
* 需先安装docker
* ARM架构的群晖安装docker参见[docker-arm-based-synology](https://github.com/412999826/docker-arm-based-synology)

安装过程需开启群晖的SSH功能，并通过`sudo -i`切换到root用户


## 拉取UniFi Controller镜像
```bash
docker pull jacobalberty/unifi
```

## 创建容器并启动

Unifi Controller的安装需要至少如下三个端口：
* 8080/tcp - 设备控制
* 8443/tcp - Web 界面 + API
* 3478/udp - STUN 服务

可前往[UniFi - Ports Used](https://help.ubnt.com/hc/en-us/articles/218506997)查看更多端口使用信息，并通过`-p ...`命令添加需要的端口。

`/volume1/homes/unifi`为UniFi Controller配置文件目录，可根据需要进行修改
```bash
sudo docker run -d --init --restart=always --name=unifi --net=bridge --volume=/volume1/homes/unifi:unifi -p 8080:8080 -p 8443:8443 -p 3478:3478/udp -e TZ='Asia/Shanghai' jacobalberty/unifi
```


## 修改端口（可选）
容器创建时如遇到端口冲突，可将冲突端口更修改为未使用端口，但是需要保持容器内外端口一致
如8080端口冲突，则可将`-p 8080:8080`改为`-p 808:808`后，重新创建容器

容器成功创建后，需修改UniFi Controller配置文件中对应的端口设置。

1. 停止容器
`docker stop unifi`

2.修改`system.properties`，文件在UniFi Controller配置文件目录data文件下
`unifi.http.port=8080` 改为 `unifi.http.port=808`，同时去掉前面的`#`注释

3.重新启动容器
`docker start unifi`

## 更新容器
停止容器
`docker stop unifi`
删除容器
`docker rm unifi`
删除所有未被挂载的卷（可选）
`docker volume prune -f`
删除镜像
`docker rmi jacobalberty/unifi`
创建容器并启动
```bash
sudo docker run -d --init --restart=always --name=unifi --net=bridge --volume=/volume1/homes/unifi:unifi -p 8080:8080 -p 8443:8443 -p 3478:3478/udp -e TZ='Asia/Shanghai' jacobalberty/unifi
```

