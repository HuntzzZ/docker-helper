version: '3.9'

services:
  moviepilot:
    container_name: moviepilot-v2
    image: jxxghp/moviepilot-v2:latest
    stdin_open: true
    tty: true
    hostname: moviepilot-v2
    networks:
      - moviepilot
    ports:
      - target: 3000
        published: 3000
        protocol: tcp
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
    environment:
      - 'PUID=0'
      - 'PGID=0'
      - 'TZ=Asia/Shanghai'
      - 'WEBUI_PORT=8081'
    volumes:
      - './qbit:/config'
      # - '/media/temp:/downloads'
      - '/media:/media'
    network_mode: host
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
    # ports:
    #   - '8096:8096'
    restart: always

networks:
  moviepilot:
    name: moviepilot
