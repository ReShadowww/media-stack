# media-stack

A stack of self-hosted media managers:
- qbittorrent -> http://localhost:5080
- Radarr -> http://localhost:7878
- Sonarr -> http://localhost:8989
- Prowlarr -> http://localhost:9696
- Jellyseerr -> http://localhost:5055
- Jellyfin -> http://localhost:8096
- Bazarr -> http://localhost:6767

## Requirements

- Docker version 23.0.5 and above
- Docker compose version v2.17.3 and above
- It may also work on some of lower versions, but its not tested.

## Install media stack and create /var/torrent-downloads folder

```
git clone https://github.com/ReShadowww/media-stack.git
cd media-stack
sudo docker network create media-stack-network
sudo docker compose up -d
```

## Get Host Address 

```
ip -4 addr show eth0 | grep -oP '(?<=inet\s)\d+\.\d+\.\d+\.\d+'
```


## Configure qBittorrent

- Open qBitTorrent at http://localhost:5080. Default username:password is admin:adminadmin
- Go to Tools --> Options --> WebUI --> Change password
- Run below commands

```
sudo docker exec -it qbittorrent bash

chown 1000:1000 /downloads/movies /downloads/tv-series
```

## Configure Radarr and Sonarr

- Open Radarr at http://localhost:7878 or Sonarr at http://localhost:8989/
- Settings --> Media Management --> Check mark "Movies deleted from disk are automatically unmonitored in Radarr" under File management section --> Save
- Settings --> Download clients --> qbittorrent --> Add Host (qbittorrent) and port (5080) --> Username and password if added --> Test --> Save
  - Host ip is:
  ```
  ip -4 addr show eth0 | grep -oP '(?<=inet\s)\d+\.\d+\.\d+\.\d+'
  ```
  - Tick box Sequential Order
  - Tick box First and Last First

## Add a movie

- Movies --> Search for a movie --> Add Root folder (/downloads) --> Quality profile --> Add movie
- Go to qbittorrent (http://localhost:5080) and see if movie is getting downloaded.

## Configure Prowlarr

- Open Prowlarr at http://localhost:9696
- Add Indexers, Indexers --> Add Indexer --> Search for indexer --> Choose base URL --> Test and Save
- Add application, Settings --> Apps --> Add application --> Choose Sonarr or Radarr or any apps to link --> Prowlarr server (http://localhost:9696) --> Radarr server (http://localhost:7878) --> API Key --> Test and Save
- This will add indexers in respective apps automatically.

## CAUTION Deletes EVERYTHING from docker, used only for test reseting
```
sudo docker stop $(sudo docker ps -a -q)
sudo docker system prune -a
sudo docker volume rm $(sudo docker volume ls -q)
sudo docker network prune
```
