# Backup and Restore

!!! warning "Advanced usage"
    These steps are done from a terminal. You need to have access to SSH and Docker to follow along. The easiest way is to have install the [SSH & Web Terminal add-on](https://community.home-assistant.io/t/home-assistant-community-add-on-ssh-web-terminal/33820).

## Backup

### Backup from Home Assistant add-on

```bash
# Enter the container
docker exec -it addon_a0d7b954_influxdb bash

# Create a (temporary) backup directory inside the container on 
# a path that's already mounted from Home Assistant
mkdir -p /share/backup/influxdb/

# Create the backup
influxd backup -database homeassistant -portable /share/backup/influxdb/

# Exit the container
exit

# (Optional) Set so all users can read the backup data
sudo chmod a+r /usr/share/hassio/share/backup/influxdb/*

# (Optional) Afterwards remove the backup
sudo rm -rf /usr/share/hassio/share/backup/influxdb/*
```

## Restore

### Restore backup to Home Assistant add-on

```bash
# Enter the container
docker exec -it addon_a0d7b954_influxdb bash

# Restore the backup from a shared directory
influxd restore -portable /share/backup/influxdb/
```

### Restore backup to Local Docker container

This can be useful for testing and debugging purposes.

```bash
# Create the directory where we'll store our backup
mkdir -p ~/dump/influxdb
cd ~/dump/influxdb

# Start by pulling the necessary Docker images
docker pull influxdb
docker pull chronograf

# Create an InfluxDB network
docker network create influxdb

# Start the InfluxDB container with a mounted path where your backup is located
docker run -d -p 8086:8086 -v $(pwd):/backup --net influxdb --name influxdb influxdb

# (Optional) Start Chronograf for a web interface
docker run -d -p 8888:8888 --net influxdb --name chronograf chronograf --influxdb-url=http://influxdb:8086
```

Chronograf provides a nice and easy to use web interface, which can be accessed on [`http://localhost:8888`](http://localhost:8888). After we have started our container(s) we can start restoring our backup.

```bash
# Retrieve our backup from Home Assistant
scp homeassistant.lan:/usr/share/hassio/share/backup/influxdb/\* ./

# Enter our InfluxDB Docker container to restore
docker exec -it influxdb bash

# And restore our backup!
influxd restore -portable /backup
```
