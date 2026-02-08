# docker-kcc

KCC wrapped in [linuxserver.io/docker-baseimage-selkies](https://github.com/linuxserver/docker-baseimage-selkies).

I will try to make a new build when the baseimage updates, but there are no
 promises.

Based on the stuff I could get to work, you have to add a path to the files you
 want to be able to convert.   
I hope to get up/download to work, so you can run it all from the browser.

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
