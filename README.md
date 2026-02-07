# docker-kcc

KCC wrapped with [linuxserver.io/docker-baseimage-selkies](https://github.com/linuxserver/docker-baseimage-selkies).

# compose-file

```yaml
services:
  kcc:
    image: ghcr.io/0rsted/lsio-kcc:latest
    container_name: kcc
    environment:
      # remember to set pUID and pGID
      - PUID=1000
      - PGID=100
      # Timezone is not required
      - TZ=Europe/Copenhagen
    volumes:
      # the path to where the files to be converted are stored
      - ./convert:/convert
    ports:
      # http port for selkies
      - 3000:3000
      # https port for selkies
      - 3001:3001
    restart: unless-stopped
```
