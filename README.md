# 这里是本人常用的一些Docker Compose配置文件

> ## 将docker run 命令转换为compose文件 宝藏网站呀
> https://www.composerize.com/
>
> https://zhuanlan.zhihu.com/p/558461211

## 一、moviepilot

```yaml
version: "3"
services:
  moviepilot:
    image: jxxghp/moviepilot:latest
    #image: ddsderek/moviepilot:latest
    #ports:
    #  - 3004:3004
    volumes:
      - /volume1/docker/moviepilot/config:/config
      - /volume1/docker/moviepilot:/moviepilot
      - /volume1/@appstore/qBittorrent/qBittorrent_conf/data/BT_backup:/BT_backup # 映射种子目录
      - /volume1/@appdata/transmission/torrents:/torrents #映射tr路径
      - /volume1/docker/nastools:/nas-tools/config # 映射NT数据
      - /volume1/docker/emby/metadata/people:/emby/metadata/people
      - /etc/localtime:/etc/localtime
      - /volume2/media:/volume2/media
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - NGINX_PORT=3000
      - PUID=0
      - PGID=0
      - UMASK=022
      - TZ=Asia/Shanghai
      - MOVIEPILOT_AUTO_UPDATE=true
      - MOVIEPILOT_AUTO_UPDATE_DEV=flase
      #- PROXY_HOST=
      #- AUTH_SITE=iyuu
      #- IYUU_SIGN=
      - AUTH_SITE=hdfans
      - HDFANS_UID=1
      - HDFANS_PASSKEY=1
    logging:
      driver: json-file
      options:
        max-size: 5m
    restart: always
    network_mode: host
    hostname: moviepilot
    container_name: moviepilot
```

## 二、auto_symlink

```yaml
name: auto_symlink
services:
    auto_symlink:
        container_name: auto_symlink
        environment:
            - TZ=Asia/Shanghai
        volumes:
            - /volume1/CloudNAS:/volume1/CloudNAS:rslave
            - /volume2/media/video/strm:/video/strm
            - /volume1/docker/auto_symlink:/app/config
        ports:
            - 8095:8095
        restart: unless-stopped
        image: shenxianmq/auto_symlink:latest
```

## 三、CloudDrive2

```yaml
version: "2.1"
services:
  cloudnas:
    image: cloudnas/clouddrive2
    container_name: clouddrive2
    environment:
      - TZ=Asia/Shanghai
      - CLOUDDRIVE_HOME=/Config
    volumes:
      - /volume1/CloudNAS:/CloudNAS:shared
      - /volume1/docker/cd2:/Config
      - /volume1/video:/media:shared #optional media path of host
    devices:
      - /dev/fuse:/dev/fuse
    restart: unless-stopped
    pid: "host"
    privileged: true
    network_mode: "host"
```

## 四、qbittorrent

```yaml
version: "3"

services:

  qbittorrent:
    image: linuxserver/qbittorrent:latest 
    container_name: qbittorrent
    environment:
      - PUID=0    
      - PGID=0    
      - TZ=Asia/Shanghai
      - WEBUI_PORT=8081
    volumes:
      - /volume1/docker/qbit:/config
      #- /volume1/docker/qbit/downloads:/downloads
      - /volume2/media:/media
     
    network_mode: host
    restart: unless-stopped
```

## 五、Joplin容器及数据库

```yaml
version: '3'

services:
    db: # 数据库
        image: postgres:13-alpine
        volumes:
            - ./data/postgres:/var/lib/postgresql/data
        expose:
            - "5432"
        restart: unless-stopped
        environment:
            - POSTGRES_PASSWORD=password # 改成你自己的密码
            - POSTGRES_USER=joplin  # 改成你自己的用户名
            - POSTGRES_DB=joplin
    app: # 程序主体
        image: joplin/server:latest
        depends_on:
            - db
        ports:
            - "22300:22300" # 左边的端口可以更换，右边不要动！
        restart: always
        environment:
            - APP_PORT=22300
            - APP_BASE_URL=http://host.xx # 改成反代的域名
            - DB_CLIENT=pg
            - POSTGRES_PASSWORD=password # 与上面的数据库密码对应！
            - POSTGRES_DATABASE=joplin
            - POSTGRES_USER=joplin  # 与上面的数据库用户名对应！
            - POSTGRES_PORT=5432
            - POSTGRES_HOST=db
            - MAX_TIME_DRIFT=0
            # SMTP设置，不需要的可以删除
            - MAILER_ENABLED=1
            - MAILER_HOST=smtp.163.com # SMTP服务器
            - MAILER_PORT=25 # 端口
            - MAILER_SECURITY=0
            - MAILER_AUTH_USER=admin # 用户名
            - MAILER_AUTH_PASSWORD=password # 密码
            - MAILER_NOREPLY_NAME=JoplinServer # 发件称呼
            - MAILER_NOREPLY_EMAIL= xxxxx@email.com # 发件邮箱
```

## 六、Clash及Clash UI

```yaml
version: '3.8'
services:
  clash:
    image: dreamacro/clash:latest
    container_name: clash
    volumes:
      - /volume1/docker/clash/config.yaml:/root/.config/clash/config.yaml
      - /volume1/docker/clash/ui:/ui # 图形面板目录
#    ports:
#      - "7890:7890"
#      - "7891:7891"
#      - "9090:9090"  
    restart: unless-stopped
    network_mode: "host"

  dashboard:
    image: haishanh/yacd:latest
    container_name: clash-dashboard
    ports:
      - "9092:80" 
    restart: unless-stopped
    network_mode: "bridge"
```

## 七、IYUU

```yaml
version: "3"

services:
  iyuuplus:
    image: iyuucn/iyuuplus:latest
    container_name: iyuuplus
    volumes:
      - /volume1/docker/IYUU/db:/IYUU/db
      - /volume1/docker/qbittorrent/config/qBittorrent/BT_backup:/BT_backup
      - /volume1/docker/transmission/config/torrents:/torrents
      - /volume1/docker/transmission/watch:/watch
    network_mode: host
    restart: unless-stopped
```

## 八、 transmission4.0+  

```yaml
version: "3"

services:
  transmission:
    image: linuxserver/transmission:latest
    container_name: transmission
    environment:
      - PUID=0
      - PGID=0
      - TZ=Asia/Shanghai
      - USER=admin
      - PASS=changeme
      - PEERPORT=10413
      - TRANSMISSION_WEB_HOME=/config/transmission-web-control
    volumes:
      - /volume1/docker/transmission/config:/config
      - /volume1//downloads:/downloads
      - /volume1/Media:/Media
    network_mode: host
    restart: unless-stopped
```

## 九、watchtower

```yaml
version: "3"

services:
  watchtower:
    image: containrrr/watchtower:latest
    container_name: watchtower
    restart: always
    environment: 
      - TZ=Asia/Shanghai
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command:
      --cleanup
      --schedule "0 0 4 * *"
```

## 十、青龙面板

```yaml
version: "3"

services:

  qinglong:
    image: whyour/qinglong:latest
    container_name: qinglong
    volumes:
       - /volume2/Media1/docker-config/qinglong/data:/ql/data
    ports:
       - 5700:5700
    restart: unless-stopped  
```

## 十一、music_tag_web

```yaml
version: '3'

services:
  music-tag:
    image: xhongc/music_tag_web:latest
    container_name: music-tag-web
    ports:
      - "8001:8001"
    volumes:
      - /volume2/media:/app/media:rw
      - /volume1/docker/music_tag_web:/app/data
    command: /start
    restart: always
```

## 十二、Halo博客

```yaml
version: "3"

services:
  halo:
    image: halohub/halo:2.12
    container_name: halo
    restart: on-failure:3
    network_mode: "host"
    volumes:
      - /volume1/docker/halo:/root/.halo2
    command:
      # 修改为自己已有的 MySQL 配置
      - --r2dbc:pool:mariadb://localhost:3306/halo
      - --spring.r2dbc.username=root
      - --spring.r2dbc.password=password
      - --spring.sql.init.platform=mysql
      # 外部访问地址，请根据实际需要修改
      - --halo.external-url=http://zzz.cc:8090/
      # 端口号 默认8090
      - --server.port=8090
```

## 十三、Parworld

```yaml
version: '3.9'

services:

  palworld-dedicated-server:
    container_name: palworld
    image: jammsen/palworld-dedicated-server:latest
    restart: always
    network_mode: bridge
    ports:
      - "8211:8211/udp"    #自定义端口，不建议改
    environment:
      - ALWAYS_UPDATE_ON_START=true    #是否更新
      - MAX_PLAYERS=6    #最大支持人数
      - MULTITHREAD_ENABLED=true    #是否开启多多线程 CPU
      - COMMUNITY_SERVER=true    #是否开启为社区服务器，如果为 true，则服务器将显示在游戏官方的社区服务器中。
      - PUBLIC_IP=10.0.0.1    #本机IP，不填则自动生成
      - PUBLIC_PORT=8211    #本机端口，和上面的端口一致，不要改
      - SERVER_NAME=Oops_xixi #自定义服务器名字
      - SERVER_DESCRIPTION=xixi_SERVER  #自定义服务器介绍
      - SERVER_PASSWORD=password  #公开服务器密码，官方有BUG，不起作用
      - ADMIN_PASSWORD=xixizzZ  #公开服务器管理员密码
    volumes:
      - /volume1/docker/palworld:/opt/palworld/Pal/Saved    #自定义数据存档路径，游戏服务器所有数据都保存在这里
```

## 十四、Xiaoya周边

DDS-Derek大佬的一键项目比compose方便
![](https://github.com/HuntzzZ/docker-helper/blob/main/img/image.png)]
https://github.com/DDS-Derek/xiaoya-alist

