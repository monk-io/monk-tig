Telegraf, InfluxDB & Grafana meets Monk
===

This repository contains Monk.io template to deploy TIG stack system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

- [Telegraf, InfluxDB \& Grafana meets Monk](#telegraf-influxdb--grafana-meets-monk)
  - [Prerequisites](#prerequisites)
    - [Make sure monkd is running.](#make-sure-monkd-is-running)
    - [Clone Repository](#clone-repository)
    - [Load Template](#load-template)
    - [Verify if it's loaded correctly](#verify-if-its-loaded-correctly)
  - [Deploy Stack](#deploy-stack)
  - [Variables](#variables)
  - [Stop, remove and clean up workloads and templates](#stop-remove-and-clean-up-workloads-and-templates)
  - [Persistency](#persistency)

## Prerequisites
- [Install Monk](https://docs.monk.io/docs/get-monk)
- [Register and Login Monk](https://docs.monk.io/docs/acc-and-auth)
- [Add Cloud Provider](https://docs.monk.io/docs/cloud-provider)
- [Add Instance](https://docs.monk.io/docs/multi-cloud)

### Make sure monkd is running.

``` bash
$ monk status
daemon: ready
auth: logged in
not connected to cluster
```

### Clone Repository

``` bash
$ git clone git@github.com:CuteAnonymousPanda/monk-tig.git
```

### Load Template

``` bash
$ cd monk-tig
monk load manifest.yaml
```

### Verify if it's loaded correctly

``` bash
$ monk list -l tigmonitoring

✔ Got the list
Type      Template                Repository  Version  Tags
runnable  tigmonitoring/grafana   local       -        -
runnable  tigmonitoring/influxdb  local       -        -
group     tigmonitoring/stack     local       -        -
runnable  tigmonitoring/telegraf  local       -        -
```

## Deploy Stack

``` bash
$ monk run tigmonitoring/stack
✔ Starting the job: local/tigmonitoring/stack... DONE
✔ Preparing nodes DONE
✔ Checking/pulling images...
✔ [================================================] 100% docker.io/grafana/grafana:latest local
✔ [================================================] 100% telegraf:1.23-alpine local
✔ [================================================] 100% influxdb:1.8 local
✔ Checking/pulling images DONE
✔ Starting containers DONE
✔ Starting containers DONE
✔ Starting containers DONE
✔ New container local-778311788dad7ef85f3dd903a1--tigmonitoring-grafana-grafana created DONE
✔ New container local-8e1a7e7656a60e3fae3ba021b1-igmonitoring-telegraf-telegraf created DONE
✔ Started local/tigmonitoring/stack

🔩 templates/local/tigmonitoring/stack
 └─🧊 Peer local
    ├─🔩 templates/local/tigmonitoring/telegraf
    │  └─📦 local-8e1a7e7656a60e3fae3ba021b1-igmonitoring-telegraf-telegraf
    │     ├─🧩 telegraf:1.23-alpine
    │     └─🔌 open udp 1.1.1.1:8125 -> 8125
    ├─🔩 templates/local/tigmonitoring/influxdb
    │  └─📦 local-04e83a69d1250f67cbc927b21f-igmonitoring-influxdb-influxdb
    │     ├─🧩 influxdb:1.8
    │     ├─💾 /var/lib/monkd/volumes/influxdb-var-lib-influxdb -> /var/lib/influxdb
    │     ├─💾 /var/lib/monkd/volumes/influxdb-etc-influxdb -> /etc/influxdb
    │     └─🔌 open 1.1.1.1:8086 -> 8086
    └─🔩 templates/local/tigmonitoring/grafana
       └─📦 local-778311788dad7ef85f3dd903a1--tigmonitoring-grafana-grafana
          ├─🧩 docker.io/grafana/grafana:latest
          └─🔌 open 1.1.1.1:3000 -> 3000

💡 You can inspect and manage your above stack with these commands:
        monk logs (-f) local/tigmonitoring/stack - Inspect logs
        monk shell     local/tigmonitoring/stack - Connect to the container's shell
        monk do        local/tigmonitoring/stack/action_name - Run defined action (if exists)
💡 Check monk help for more!
```

## Variables

The variables are stored in `manifest.yaml` file.
You can quickly setup by editing the values there.

| Variable                 | Description                                  | Default                                               |
| ------------------------ | -------------------------------------------- | ----------------------------------------------------- |
| influx-host              | Internal variable to determine influxdb host | <- get-hostname("tigmonitoring/influxdb", "influxdb") |
| influx-admin-user        | Influxdb admin username                      | admin                                                 |
| influx-admin-pass        | Influxdb admin password                      | adminz                                                |
| influx-http-auth-enabled | Influxdb enable authentication               | true                                                  |
| grafana-admin-user       | Default grafana Administrator username       | admin                                                 |
| grafana-admin-password   | Default grafana Administrator password       | adminz                                                |
| grafana-install-plugins  | Default grafana plugins to install           | grafana-clock-panel,grafana-simple-json-datasource    |
| grafana-anonymous        | Should we enable anonymous access            | true                                                  |
| grafana-anonymous-role   | Role of the anonymous user                   | Viewer                                                |

## Stop, remove and clean up workloads and templates

``` bash
monk purge    tigmonitoring/grafana tigmonitoring/influxdb tigmonitoring/stack tigmonitoring/telegraf
monk purge -x tigmonitoring/grafana tigmonitoring/influxdb tigmonitoring/stack tigmonitoring/telegraf
```

## Persistency
If you're using any of the clouds available via Monk. You can use volume definition to spin a disk block device to make your TIG instance independent from the node it's running on.
To do simply uncomment the `volume` blocks in `manifest.yaml`
