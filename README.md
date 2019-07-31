# Cloud scale messaging and IoT connectivity with AMQ Online 

## Pre-requisites

Install the `oc` command line application: https://docs.openshift.com/container-platform/3.11/cli_reference/get_started_cli.html#installing-the-cli

Download and install to your local machine:

* https://github.com/ctron/hat/releases
* https://github.com/ctron/hot/releases (be sure to use a 0.4.x version)

In the following examples the tools will be references to as `hat` and `hot`, so please rename them accordingly after downloading.

## Generic messaging

This part of the tutorial will focus on generic messaging. It will show case
a "stock ticker" application, which has a single publisher of prices and
multiple consumers.

See: [stage1](stage1/) 

## IoT connectivity

This part of the tutorial will combine generic messaging and IoT
connectivity to build an application with gathers weather information
and provides a dashboard with the aggregated value.

**Note:** The IoT functionality of AMQ Online is still tech preview.

See: [stage2](stage2/)

## See also

The tutorial will use code from the following repositories:

* https://github.com/ctron/amq-online-example-iot-camel-consumer
* https://github.com/ctron/amq-online-example-iot-camel-publisher
* https://github.com/ctron/amq-online-example-iot-mariadb
* https://github.com/ctron/amq-online-example-iot-weather
* https://github.com/ctron/amq-online-example-stock-consumer
* https://github.com/ctron/amq-online-example-stock-publisher
