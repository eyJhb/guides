# Docker
## Volumes vs bind mounts
When using true volumes, it is specified as

```
version: "3"
services:
  ftpd-server:
    container_name: ftpd-server
    image: stilliard/pure-ftpd
    restart: always
    volumes:
      - ftpUserVolume:/home/ftpusers/
    ports:
      - "21:21"
      - "30000-30009:30000-30009"
    environment:
      - PUBLICHOST=192.168.1.11
volumes:
  ftpUserVolume:
```

This means that the volume will be saved at some location Docker decides.
Bind volumes you control where the data is from, on your local computer, and will look like (usefull for using external devices).

```
version: "3"
services:
  ftpd-server:
    container_name: ftpd-server
    image: stilliard/pure-ftpd
    restart: always
    volumes:
      - ${USERDIR}/docker/ftp-data/:/home/ftpusers/
    ports:
      - "21:21"
      - "30000-30009:30000-30009"
    environment:
      - PUBLICHOST=192.168.1.11
```

## Exposing volumes in Dockerfile
When exposing a volume in Dockerfile, it means that the data should NOT be in the container.
The directory is then created inside the container, and then also outside the container where Docker seems fit.
This could be for reasons like the above, where you do not want all the data inside the container (ftp user data), and do not trust the user to setup the volumes themselves.
Could also be because of log files...

## Health checks
The docker container will not be part of dockers internal DNS, until it is marked healthy, which can take some time.
So for the first few seconds, it can seem unresponsive.

NOTE: may only apply to swarm setups
