+++
title = "docker_new"
description = "Short description"
date = 2019-07-18T15:24:26+08:00
draft = false
+++

## docker {#docker}


### 文件传输 {#文件传输}

docker容器和主机互相拷贝传输文件

```nil
docker cp 0000:/home/ /tmp/
```

宿主到docker容器

```nil
cp /tmp/ xxx:/home/
```