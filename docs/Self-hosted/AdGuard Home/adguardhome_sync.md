# AdGuard Home Sync

In addition to my main [AdGuard Home](https://adguard.com/adguard-home/overview.html) install, I also have deployed a secondary instance of AdGuard Home. This is automatically synced using [AdGuard Home Sync](https://github.com/bakito/adguardhome-sync).

## Compose

```yaml
services:
  adguardhome:
    image: adguard/adguardhome
    container_name: adguardhome
    restart: always
    pull_policy: always
    ports:
      - 192.168.1.5:53:53/tcp
      - 192.168.1.5:53:53/udp
      - 3000:3000/tcp

  adguardhome-sync:
    image: ghcr.io/bakito/adguardhome-sync
    container_name: adguardhome-sync
    command: run
    restart: always
    pull_policy: always
    ports:
      - 3001:8080
    environment:
      ORIGIN_URL: 'http://192.168.1.4:3000'

      REPLICA_URL: 'http://192.168.1.5:3000'
      REPLICA_AUTOSETUP: true
      REPLICA_USERNAME: admin
      REPLICA_PASSWORD: password

      CRON: '*/10 * * * *' # run every 10 minutes    
      RUNONSTART: true
```

## Notes

!!! warning inline end 

    These notes were checked and updated on 2023-04-07.

### AdGuard Home Sync WebUI

AdGuard Home Sync also has a WebUI. Although I have this mapped, this is entirely optional.

### Authentication

#### Origin authentication

The origin AdGuard Home doesn't have authentication enabled.

#### Replica authentication

I couldn't find an option to disable authentication on the replica and still use auto setup.

### Disabled blocklists

Disabled blocklists were copied and enabled on the replica.
