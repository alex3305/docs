# InfluxDB

I use [InfluxDB](https://www.influxdata.com/) time-series database for storing all my sensor data. This database is easy to configure and there is a Home Assistant [add-on](https://github.com/hassio-addons/addon-influxdb) available.

## Add-on configuration

I use the default configuration from the add-on. Within InfluxDB I configure the following:

* **Database** `homeassistant`
* **User** `homeassistant`

### Home Assistant Configuration

After configuring the database and user, I add the [InfluxDB integration](https://www.home-assistant.io/integrations/influxdb/) to Home Assistant by adding it to my `configuration.yaml`:

!!! info inline end
    I've excluded some domains and sensors that I won't be querying anyway

```yaml
influxdb:
  host: a0d7b954-influxdb
  port: 8086
  database: homeassistant
  username: homeassistant
  password: !secret influxdb_password
  max_retries: 3
  default_measurement: state
  exclude:
    domains:
      - zwave
      - automation
    entities:
      - sensor.date
      - sensor.date_time
      - sensor.time
```
