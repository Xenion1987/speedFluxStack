# Docker InfluxDB + Telegraf + Grafana + SpeedFlux

> ## This Readme is still a draft and has to be updated

## Create config files
In case you want to customize service config files, that's how you get the default values.
Create directories, first:
```sh
mkdir -p influxdb/{data,conf} telegraf/conf grafana/{data,provisioning}
```
### influxdb
```sh
docker run --rm influxdb:2.1 influxd print-config > influxdb/conf/config.yml
```
### telegraf
```sh
docker run --rm telegraf cat /etc/telegraf/telegraf.conf > telegraf/conf/telegraf.conf
```

### Grafana
```sh
docker run --rm --entrypoint /bin/bash grafana/grafana-oss:latest -c 'cat $GF_PATHS_CONFIG' > ./grafana/conf/grafana.ini
```

## Update telegraf config to enable InfluxDBv2
Comment in at least those lines and update them as shown below:
```toml
[[outputs.influxdb]]
  urls = ["http://influxdb:8086"]
  database = "${INFLUXDB_DB}" 
  username = "${INFLUXDB_USER}"
  password = "${INFLUXDB_USER_PASSWORD}"
```
Replace all variables with the final values.

## Modify `.env`-File
1. `cp .env-example .env`
1. Modify varaibles to fit your needs



# **DRAFT** :: InfluxDBv2 :: **DRAFT**
## Create and update influxdb token
1. Start the stack
1. Open the influxdb web frontend `http://[IP-ADRESS]:[PORT]` (default: `http://localhost:8086`)
1. Login using your credentials
1. Create a new token
    1. Load Data > API Tokens > <kbd>+ Generate API Token</kbd> > Read/Write API Token
    ![./assets/generate_token.png](./assets/generate_token.png)
1. Copy token to `telegraf.conf`
1. Restart stack via `docker-compose restart`

## Add InfluxDB as Grafana Datasource
1. Login with your admin credentials
1. Configuration > Data sources > <kbd>Add data source</kbd> > InfluxDB
    1. Query Language: `Flux`
    1. URL: `http://[INFLUXDB_DOCKER_IP]:8086`
    1. InfluxDB Details
        1. Organization: `${INFLUXDB_ORG}`
        1. Token: `${INFLUXDB_API_TOKEN}`
        1. Default Bucket: `${INFLUXDB_BUCKET}`
    1. <kbd>Save & test</kbd>
