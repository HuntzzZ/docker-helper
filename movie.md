```bash
mkdir -p moviepilot-v2 qbit tailscale && chmod -R 777 moviepilot-v2 qbit tailscale
cd moviepilot-v2 && mkdir -p config core
cd .. && wget https://dir.luzzzz.online/%E5%85%B6%E4%BB%96/emby.zip
unzip emby.zip
wget https://dir.luzzzz.online/auto_compose.yaml
cd tailscale && mkdir -p state dev
```

```yaml
version: '3.0'

services:
  moviepilot:
    container_name: moviepilot-v2
    image: jxxghp/moviepilot-v2:latest
    stdin_open: true
    tty: true
    hostname: moviepilot-v2
    network_mode: host
    volumes:
      - '/media:/media'
      - './moviepilot-v2/config:/config'
      - './moviepilot-v2/core:/moviepilot/.cache/ms-playwright'
      - '/var/run/docker.sock:/var/run/docker.sock:ro'
    environment:
      - 'NGINX_PORT=3000'
      - 'PORT=3001'
      - 'PUID=0'
      - 'PGID=0'
      - 'UMASK=000'
      - 'TZ=Asia/Shanghai'
      - 'AUTH_SITE=iyuu'
      - 'IYUU_SIGN=xxxx'
      - 'SUPERUSER=admin'
    restart: always

  qbittorrent:
    container_name: qbittorrent
    image: linuxserver/qbittorrent:latest
    network_mode: host
    environment:
      - 'PUID=0'
      - 'PGID=0'
      - 'TZ=Asia/Shanghai'
      - 'WEBUI_PORT=8081'
    volumes:
      - './qbit:/config'
      - '/media:/media'
    restart: always

  embyserver:
    container_name: embyserver
    image: amilys/embyserver:beta
    network_mode: host
    environment:
      - 'PUID=0'
      - 'PGID=0'
      - 'TZ=Asia/Shanghai'
    volumes:
      - './emby:/config'
      - '/media:/media'
    devices:
      - '/dev/dri:/dev/dri'
    restart: always

  tailscale:
    container_name: tailscale
    network_mode: host
    volumes:
      - './state:/var/lib/tailscale'
      - './dev:/dev/net/tun'
    environment:
      - 'TS_AUTHKEY=tskey-auth-xxxx'
      - 'TS_EXTRA_ARGS=--advertise-exit-node'
      - 'TS_ROUTES=192.168.1.0/24'
      - 'TS_HOSTNAME=FnOS'
      - 'TS_STATE_DIR=/var/lib/tailscale'
    restart: unless-stopped
    image: tailscale/tailscale

 sakura1:
    container_name: sakurafrpc
    image: natfrp.com/frpc
    restart: on-failure:5
    network_mode: host
    command: --disable_log_color -f <启动参数>

networks:
  moviepilot:
    name: moviepilot

```
