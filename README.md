# InfluxData 1.x Sandbox

This repo is a quick way to get the entire 1.x TICK Stack spun up and working together. It uses [Docker](https://www.docker.com/) to spin up the full TICK stack in a connected 
fashion. This is heavily tested on MacOS and should mostly work on Linux and Windows.

### Running

To run the `sandbox`, simply use the convenient cli:

```bash
$ ./sandbox
sandbox commands:
  up           -> spin up the sandbox environment (add -nightly to grab the latest nightly builds of InfluxDB and Chronograf)
  down         -> tear down the sandbox environment
  restart      -> restart the sandbox
  influxdb     -> attach to the influx cli
  flux         -> attach to the flux REPL

  enter (influxdb||kapacitor||chronograf||telegraf||grafana) -> enter the specified container
  logs  (influxdb||kapacitor||chronograf||telegraf||grafana) -> stream logs for the specified container

  delete-data  -> delete all data created by the TICK Stack
  docker-clean -> stop and remove all running docker containers
  rebuild-docs -> rebuild the documentation container to see updates
```

To get started just run `./sandbox up`. You browser will open two tabs:

- `localhost:8888` - Chronograf's address.
- `localhost:3000` - Grafana's address.
- `localhost:3010` - Documentation server. This contains a simple markdown server for tutorials and documentation.

> NOTE: Make sure to stop any existing installations of `influxdb`, `kapacitor` or `chronograf`. If you have them running the Sandbox will run into port conflicts and fail to properly start. In this case stop the existing processes and run `./sandbox restart`. Also make sure you are **not** using _Docker Toolbox_.

You are ready to get started with the TICK Stack!

> Note: see [influxdata/inch](https://github.com/influxdata/inch) to create data for your Sandbox, Inch is an InfluxDB benchmarking tool for testing with different tag cardinalities. It is included in the influxdb container.

### Configuring
#### Telegraf
Place input configurations for telegraf under `/telegraf/telegraf.d` directory. Telegraf will automatically reload when configurations are made/added to this directory.'

Telegraf will handle creating the InfluxDB database (defined under `/telegraf/telegraf.conf`).

Telegraf will immediately start creating mock metrics, which can be modified `/telegraf/telegraf.d/mock_metrics.conf`. 

This allows for easier testing of kapacitor alerts. You can also collect metrics outside of this sandbox. For example, if you need to collect metrics from a device using SNMP, you can put the MIB files under `/telegraf/.snmp`.
#### Kapacitor

There are two ways to provide your tick scripts to kapacitor.
1. Open Chronograf in your browser, hover over the 'alert' icon in the nav bar, and select the TICK script dropdown option. Chronograf will automatically reload kapacitor so there is no need to restart the container for changes to take effect.
2. Place TICK scripts under `/kapacitor/config/load/tasks` directory. Kapacitor requires a `./sandbox restart kapacitor` to process script changes.

Likewise, there are two ways to validate your TICK script.
1. Use Chronograf. Once you hit the save button, Chronograf will let you know if there is a problem witht he syntax. Chronograf and tickfmt do not check for mistyped measurements. If you mispell a tag/field/measurement, you will have to look to `kapacitor watch <task_name>` or `./sandbox logs kapacitor` to see the error.
2. Enter kapacitor and use the `tickfmt` command.


After entering the Kapcitor container, use `kapacitor watch <task_name>` to monitor the triggering of alert and their resolutions.

#### Grafana
Place dashboards under `/grafana/dashboards` sandbox directory. Grafana will update the list of dashboards every 5 seconds, just refresh the page.
#### InfluxDB
Sample data needs to be made, or just use telegraf to create. `inch` can also be used to some extent.
