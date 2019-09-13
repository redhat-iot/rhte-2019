# IoT weather application

* Edge devices gather information
* Publish to the cloud
* Cloud backend aggregates
* Publishes aggregated result
* Edge devices consume aggregated result

**Note:** The IoT functionality of AMQ Online is still *tech preview*.

## Introduction

### Tenant, IoT Tenant, Namespace, Project, and IoT Project

Naming things is tricky, especially when the work of different, independent groups
comes together in a overarching project like AMQ Online.

* **Tenant** EnMasse, the upstream project of AMQ Online, has the concept of tenants. Tenants are isolated from
             each other, and each of the tenants can have multiple *address spaces*, *addresses* and *messaging users*.
* **IoT Tenant** The IoT connectivity layer "Hono", which is the base for AMQ Online's IoT layer,
                 also has the concept of a *tenant*. Unfortunately it doesn't match nicely into the
                 concept of EnMasse tenants. Tenants are isolated, but each tenant only has a base set of messaging
                 resources available.
* **Namespace** Kubernetes has the concept of a *namespace*, which isolates resources in a kind of group.
* **Project** OpenShift has the concept of a *project*, which builds on top of a Kubernetes *namespace*, but adds a bit
              on top of it.
* **IoT Project** In the context of AMQ Online, an "IoT Project" is an "IoT Tenant". As AMQ Online allows each of its
                  tenants to have multiple messaging resources, it also allows each tenant, to have multiple "IoT tenants"
                  aka "IoT projects".

### The name of the tenant

On the context of AMQ Online IoT/Hono the name of an (IoT) tenant is the combination of the IoT project name
and the Kubernetes namespace.

Assuming you create a new IoT project named `iot` in the OpenShift project `k8s`, the Hono Tenant/IoT tenant's
name would be `k8s.iot`.

## Deploying

Unfortunately you cannot simply deploy the whole application in one big step. The next sections
describe how to deploy this tutorial.

### IoT project

The first component to deploy is the IoT project, you can do this by executing:

    oc apply -f 010-iot

This will deploy the IoT project, which will also create a new address space (named `iot`).

**Note**: As the address space is a pre-requisite for the address and the users, it may happen
that the requests are executed "too fast" and so the address and/or the user creation may fail.
In this case, simply wait a bit and re-try `oc apply`.

### Messaging resources

The second component to deploy is an additional set of messaging resources. We will
use these messaging resources to publish the aggregated results from the IoT aggregation application:

    oc apply -f 020-messaging

This will create a new address space named `info`, including an address and messaging users.

**Note**: As the address space is a pre-requisite for the address and the users, it may happen
that the requests are executed "too fast" and so the address and/or the user creation may fail.
In this case, simply wait a bit and re-try `oc apply`.

### Aggregation application

The aggregation application consists of two processes. One consumes the IoT messages, from the `iot`
address space, and stores them in a MySQL database. The second process uses MySQL to build an average
value over the messages from the last seconds, and publishes the average to the `info` address space.

You will need to update the following environment variables:

* In `030-aggregation/030-DeploymentConfig-consumer.yaml`
  ** `IOT_TENANT` with your tenant name (`<namespace>.iot`).
  ** `AMQP_HOST` with the output of (❗`iot` address space❗):
     
     ~~~
     oc get addressspace iot -o jsonpath='{ .status.endpointStatuses[?(@.name=="messaging")].serviceHost }'
     ~~~
* In `030-aggregation/030-DeploymentConfig-publisher.yaml`
  ** `AMQP_HOST` with the output of (❗`info` address space❗):
     
     ~~~
     oc get addressspace info -o jsonpath='{ .status.endpointStatuses[?(@.name=="messaging")].serviceHost }'
     ~~~

Then deploy everything using:

    oc apply -f 030-aggregation

### Dashboard application

You will need to update the following environment variables:

* In `040-dashboard/030-DeploymentConfig.yaml`
  ** `AMQP_URI` with the output of (❗`info` address space❗):
     
     ~~~
     oc get addressspace info -o jsonpath='{ .status.endpointStatuses[?(@.name=="messaging-wss")].externalHost }'
     ~~~

Then deploy the dashboard by executing:

    oc apply -f 040-dashboard

## Testing

### Register a new device

    hat context create tutorial-iot https://device-registry-enmasse-infra.apps.wonderful.iot-playground.org --default-tenant rhte2019.iot
    hat reg create device1
    hat cred set-password auth1 sha-256 pwd1 --device device1

### Publish as device

You can emulate a device using the tool `hot`. This allows you to publish data, like an IoT device
would normally do.

    hot publish http telemetry https://iot-http-adapter-enmasse-infra.apps.wonderful.iot-playground.org rhte2019.iot device1 auth1 pwd1 '{"temp":20.0}' -t "application/json"

Or using MQTT:

    hot publish mqtt telemetry https://iot-mqtt-adapter-enmasse-infra.apps.wonderful.iot-playground.org rhte2019.iot device1 auth1 pwd1 '{"temp":20.0}'
