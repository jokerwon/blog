---
title: Docker
tag: [Devops]
---

<!-- more -->

docker images 查看 images 列表，等价于 dacker image ls

docker container ls 查看运行中的容器，加 `-a` 查看所有容器

docker rm <container> 删除容器 等价于 docker container rm <container>

docker rmi <image> 删除镜像 等价于 docker image rm <image>

docker cp

容器与主机之间的数据拷贝。

docker  cp  <container>:<container_path>  <host_path> 从容器中拷贝文件到宿主机

docker  cp <host_path>  <container>:<container_path> 从宿主机拷贝文件到容器

docker container exec -it d7c7 /bin/bash
