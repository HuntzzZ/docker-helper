# Docker 常用命令

## 目录

1. [启动类](#一、启动类)
2. [镜像类](#二镜像类)
3. [容器类](#三容器类)
4. [网络类](#四网络类)
5. [Docker Compose](#五docker-compose)
6. [其他](#六其他)

------

### 一、启动类

1. 启动 Docker

   ```bash
   systemctl start docker
   ```

2. 关闭 Docker

   ```bash
   systemctl stop docker
   ```

3. 重新启动 Docker

   ```bash
   systemctl restart docker
   ```

4. Docker 设置自启动

   ```bash
   systemctl enable docker
   ```

5. 查看 Docker 运行状态

   ```bash
   systemctl status docker
   ```

6. 查看 Docker 版本号等信息

   ```bash
   docker version
   ```

   或者

   ```bash
   docker info
   ```

7. Docker 帮助

   ```bash
   docker --help
   ```

   查看特定命令的帮助，例如：

   ```bash
   docker run --help
   ```

### 二、镜像类

1. 查看镜像

   ```bash
   docker images
   ```

2. 搜索镜像

   ```bash
   docker search [OPTIONS] 镜像名字
   ```

   例如：

   ```bash
   docker search mysql
   ```

3. 拉取镜像

   ```bash
   docker pull [OPTIONS] 镜像名[:TAG]
   ```

   例如，默认拉取最新版：

   ```bash
   docker pull mysql
   ```

4. 运行镜像

   ```bash
   docker run [OPTIONS] 镜像名[:TAG] [COMMAND] [ARG...]
   ```

   例如，运行 Tomcat 容器：

   ```bash
   docker run -d --name tomcat tomcat
   ```

5. 删除镜像

   ```bash
   docker rmi [OPTIONS] 镜像名或ID
   ```

   例如，强制删除：

   ```bash
   docker rmi -f mysql
   ```

6. 加载镜像

   ```bash
   docker load [OPTIONS]
   ```

   从文件加载镜像，例如：

   ```bash
   docker load -i myimage.tar
   ```

7. 保存镜像

   ```bash
   docker save [OPTIONS] 镜像名[:TAG] > 保存位置和名字
   ```

   例如：

   ```bash
   docker save -o myimage.tar tomcat
   ```

### 三、容器类

1. 查看正在运行的容器

   ```bash
   docker ps
   ```

   查看所有容器：

   ```bash
   docker ps -a
   ```

2. 创建容器

   ```bash
   docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
   ```

   常用参数：

   - `--name=NAME`：为容器指定名字。
   - `-d`：后台运行容器。
   - `-i`：以交互模式运行容器。
   - `-t`：为容器重新分配一个伪输入终端。
   - `-P`：随机端口映射。
   - `-p`：指定端口映射。
     例如，创建并运行 Nginx 容器：

   ```bash
   docker run -d --name nginx -p 80:80 nginx
   ```

3. 启动守护式容器（后台运行）

   ```bash
   docker run -d 容器名
   ```

   例如：

   ```bash
   docker run -d redis:6.0.8
   ```

4. 停止容器

   ```bash
   docker stop 容器名
   ```

   例如：

   ```bash
   docker stop nginx
   ```

5. 启动容器

   ```bash
   docker start 容器名
   ```

   例如：

   ```bash
   docker start nginx
   ```

6. 进入正在运行的容器

   ```bash
   docker exec -it 容器名 /bin/bash
   ```

   例如：

   ```bash
   docker exec -it nginx /bin/bash
   ```

7. 强制停止容器

   ```bash
   docker kill 容器名
   ```

   例如：

   ```bash
   docker kill nginx
   ```

8. 删除容器

   ```bash
   docker rm [OPTIONS] 容器ID或名称
   ```

   例如，强制删除：

   ```bash
   docker rm -f nginx
   ```

9. 查看容器日志

   ```bash
   docker logs 容器名
   ```

   例如：

   ```bash
   docker logs nginx
   ```

10. 查看容器内运行的进程

    ```bash
    docker top 容器名
    ```

    例如：

    ```bash
    docker top nginx
    ```

11. 查看容器内部细节

    ```bash
    docker inspect 容器名
    ```

    例如：

    ```bash
    docker inspect nginx
    ```

12. 创建容器数据卷挂载

    ```bash
    docker run -d --name nginx -p 80:80 -v html:/usr/share/nginx/html nginx
    ```

13. 查看数据卷

    ```bash
    docker volume ls
    ```

14. 查看数据卷详情

    ```bash
    docker volume inspect 数据卷名
    ```

    例如：

    ```bash
    docker volume inspect html
    ```

15. 删除数据卷

    ```bash
    docker volume rm 数据卷名
    ```

    例如：

    ```bash
    docker volume rm html
    ```

### 四、网络类

1. 查看网络

   ```bash
   docker network ls
   ```

2. 创建网络

   ```bash
   docker network create 网络名
   ```

   例如：

   ```bash
   docker network create hmall
   ```

3. 查看网络数据源

   ```bash
   docker network inspect 网络名
   ```

   例如：

   ```bash
   docker network inspect hmall
   ```

4. 删除网络

   ```bash
   docker network rm 网络名
   ```

   例如：

   ```bash
   docker network rm hmall
   ```

### 五、Docker Compose

1. 查看帮助

   ```bash
   docker-compose -h
   ```

2. 启动所有服务

   ```bash
   docker-compose up
   ```

   后台运行：

   ```bash
   docker-compose up -d
   ```

3. 停止并删除容器、网络、卷、镜像

   ```bash
   docker-compose down
   ```

4. 进入容器实例内部

   ```bash
   docker-compose exec 服务名 /bin/bash
   ```

5. 展示容器

   ```bash
   docker-compose ps
   ```

6. 展示进程

   ```bash
   docker-compose top
   ```

7. 查看容器输出日志

   ```bash
   docker-compose logs 服务名
   ```

8. 检查配置

   ```bash
   docker-compose config
   ```

   有问题才有输出：

   ```bash
   docker-compose config -q
   ```

9. 启动服务

   ```bash
   docker-compose start
   ```

10. 重启服务

    ```bash
    docker-compose restart
    ```

11. 停止服务

    ```bash
    docker-compose stop
    ```

### 六、其他

1. 命令别名
   修改 `~/.bashrc` 文件：

   ```bash
   vi ~/.bashrc
   ```

   添加以下内容：

   ```bash
   # User specific aliases and functions
   
   alias rm='rm -i'
   alias cp='cp -i'
   alias mv='mv -i'
   alias dps='docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"'
   alias dis='docker images'
   
   # Source global definitions
   if [ -f /etc/bashrc ]; then
       . /etc/bashrc
   fi
   ```

   保存并退出：

   ```bash
   :wq
   ```

   执行命令使别名生效：

   ```bash
   source ~/.bashrc
   ```

以上是 Docker 常用命令，确保每个命令都是有效的，并提供了一些示例和说明，希望这能帮助你更好地管理和使用Docker。
