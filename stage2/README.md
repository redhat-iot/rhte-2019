## Pre-requisites

Download and install to your local machine:

* https://github.com/ctron/hat/releases
* https://github.com/ctron/hot/releases (be sure to use a 0.4.x version)

In the following examples the tools will be references to as `hat` and `hot`, so please rename them accordingly after downloading.

## Create connection to AMQ Online IoT

Create a new connection:

* **Connection URI:** `amqps://messaging-ezjvdj201q.enmasse-infra.svc:5671`
* **User Name:** `consumer`
* **Password:**: `test12`
* **Check Certificates**: `false` (*sigh*)

Next page:

* **Connection Name:** `iot`

## Create connection to MariaDB

Create a new connection:

* **Connection URI:** `jdbc:mysql://mariadb.rhte2019.svc:3306/iot`
* **Username:** `user`
* **Password:** `password`

## New Integration

Create a new integration:

### Source

Use "Subscribe for messages":

* **Destination Name**: `telemetry/rhet2019.iot`
* **Destination Type**: `topic`

Output data type:

* **Type**: JSON Instance
* **Definition**: `{"temp":1.2}`
* **Data Type Name**: `device-payload`

## Register a new device

    hat context create tutorial-iot https://device-registry-enmasse-infra.apps.wonderful.iot-playground.org --default-tenant rhte2019.iot
    hat reg create device1
    hat cred set-password auth1 sha-256 pwd1 --device device1

## Publish as device

    hot publish http telemetry https://iot-http-adapter-enmasse-infra.apps.wonderful.iot-playground.org rhte2019.iot device1 auth1 pwd1 '{"temp":20.0}' -t "application/json"

