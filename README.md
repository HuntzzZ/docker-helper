# 这里是本人常用的一些Docker Compose配置文件

> ## 将docker run 命令转换为compose文件 宝藏网站呀
> https://www.composerize.com
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
    #  - 3004:3000
    volumes:
      - /volume1/docker/moviepilot/config:/config
      - /volume1/docker/moviepilot/core:/moviepilot/.cache/ms-playwright
      - /volume1/@appstore/qBittorrent/qBittorrent_conf/data/BT_backup:/BT_backup # 映射种子目录
      - /volume1/@appdata/transmission/torrents:/torrents #映射tr路径
      - /volume1/docker/nastools:/nas-tools/config # 映射NT数据
      - /etc/localtime:/etc/localtime
      - /volume2/media:/volume2/media
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - NGINX_PORT=3000
      - PUID=0
      - PGID=0
      - UMASK=000
      - API_TOKEN=moviepilot
      - TZ=Asia/Shanghai
      - MOVIEPILOT_AUTO_UPDATE=true
      - MOVIEPILOT_AUTO_UPDATE_DEV=flase
      #- PROXY_HOST=
      #- GITHUB_TOKEN=
      - GITHUB_PROXY=https://mirror.ghproxy.com/
      - AUTH_SITE=iyuu
      - IYUU_SIGN=
      #- AUTH_SITE=hdfans
      #- HDFANS_UID=1
      #- HDFANS_PASSKEY=1
    restart: always
    network_mode: host
    hostname: moviepilot
    container_name: moviepilot
```

```yaml
version: "3"
services:
  moviepilot-v2:
    image: jxxghp/moviepilot-v2:latest
    #ports:
    #  - 3004:3000
    volumes:
      - /volume1/docker/moviepilot/config:/config
      - /volume1/docker/moviepilot/core:/moviepilot/.cache/ms-playwright
      - /volume1/@appstore/qBittorrent/qBittorrent_conf/data/BT_backup:/BT_backup # 映射种子目录
      #- /volume1/@appdata/transmission/torrents:/torrents #映射tr路径
      - /etc/localtime:/etc/localtime
      - /volume2/media:/volume2/media
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - NGINX_PORT=3000
      - PUID=0
      - PGID=0
      - UMASK=022
      - TZ=Asia/Shanghai
      #- MOVIEPILOT_AUTO_UPDATE=true
      #- MOVIEPILOT_AUTO_UPDATE_DEV=flase
      #- PROXY_HOST=
      #- GITHUB_PROXY=https://mirror.ghproxy.com/
      - AUTH_SITE=iyuu
      - IYUU_SIGN=
      #- AUTH_SITE=hdfans
      #- HDFANS_UID=1
      #- HDFANS_PASSKEY=1
    restart: always
    network_mode: host
    hostname: moviepilot
    container_name: moviepilot-v2
```
企业微信推送api：https://IP:PORT/api/v1/message/?token=


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
```
mount --bind /volume1/CloudNAS /volume1/CloudNAS
mount --make-shared /volume1/CloudNAS
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
      - ./config:/config
      - ./watch:/watch
      - /path/downloads:/downloads
      - /path/media:/media
    network_mode: host
    restart: unless-stopped
```
webUI地址：[https://github.com/jayzcoder/TrguiNG](https://github.com/jayzcoder/TrguiNG)

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
### DDS大佬小雅emby项目
DDS-Derek大佬的一键项目比compose方便
![](https://github.com/HuntzzZ/docker-helper/blob/main/img/image.png)
- 地址：[https://github.com/DDS-Derek/xiaoya-alist](https://github.com/DDS-Derek/xiaoya-alist)

### AI老G 脚本推荐
> - 小雅全家桶安装脚本（支持AI老G版小雅Alist安装，Jellyfin安装，快速Emby安装）:
> 
> - `bash <(curl -sSLf https://xy.ggbond.org/xy/xy_install.sh)`
> 
> - 备用：
> - `bash -c "$(curl -sSLf https://xy.ggbond.org/xy/xy_install.sh)"`

- 玩客云刷casaos小雅emby全家桶:
> `bash <(curl -sSLf https://xy.ggbond.org/xy/wky_xy_emby_ailg.sh)`


## 十五、Lucky

```yaml
services:
    lucky:
        container_name: lucky
        restart: always
        network_mode: host
        volumes:
            - /volume1/docker/lucky:/goodluck
        image: gdy666/lucky
```

## 十六、tailscale
```yaml
version: '3.7'
services:
    tailscale:
        container_name: tailscale
        volumes:
            - ./state:/var/lib/tailscale
            - ./dev:/dev/net/tun
        network_mode: host
        restart: unless-stopped
        environment:
            - TS_AUTHKEY=tskey-auth-xxxx    #填上一步生成的 Auth key
            - TS_EXTRA_ARGS=--advertise-exit-node
            - TS_ROUTES=192.168.1.0/24   #把xx替换成自己网关的网段
            - TS_HOSTNAME=FnOS    #把xx替换成自己喜欢的名字，比如 fnOS
            - TS_STATE_DIR=/var/lib/tailscale
        image: tailscale/tailscale
```

## 十七、Umbrel
```yaml
services:
  umbrel:
    image: dockurr/umbrel
    container_name: umbrel
    pid: host
    ports:
      - 80:80
    volumes:
      - "/home/example:/data"
      - "/var/run/docker.sock:/var/run/docker.sock"
    stop_grace_period: 1m
```
```bash
docker run -it --rm -p 80:80 -v /home/example:/data -v /var/run/docker.sock:/var/run/docker.sock --pid=host --stop-timeout 60 dockurr/umbrel
```

## 十八、NPS
安装
docker pull duan2001/nps

配置
创建并修改 [nps.conf](https://github.com/djylb/nps/blob/master/conf/nps.conf)⁠ 配置文件

启动
```
docker run -d --restart=always --name nps --net=host -v <本机conf目录>:/conf -v /etc/localtime:/etc/localtime:ro duan2001/nps
```
