# IoT weather application

* Edge devices gather information
* Publish to the cloud
* Cloud backend aggregates
* Publishes aggregated result
* Edge devices consume aggregated result

**Note:** The IoT functionality of AMQ Online is still *tech preview*.

## Tenant, IoT Tenant, Namespace, Project, and IoT Project

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

## The name of the tenant

On the context of AMQ Online IoT/Hono the name of an (IoT) tenant is the combination of the IoT project name
and the Kubernetes namespace.

Assuming you create a new IoT project named `iot` in the OpenShift project `k8s`, the Hono Tenant/IoT tenant's
name would be `k8s.iot`.

## Register a new device

    hat context create tutorial-iot https://device-registry-enmasse-infra.apps.wonderful.iot-playground.org --default-tenant rhte2019.iot
    hat reg create device1
    hat cred set-password auth1 sha-256 pwd1 --device device1

## Publish as device

You can emulate a device using the tool `hot`. This allows you to publish data, like an IoT device
would normally do.

    hot publish http telemetry https://iot-http-adapter-enmasse-infra.apps.wonderful.iot-playground.org rhte2019.iot device1 auth1 pwd1 '{"temp":20.0}' -t "application/json"

Or using MQTT:

    hot publish mqtt telemetry https://iot-mqtt-adapter-enmasse-infra.apps.wonderful.iot-playground.org rhte2019.iot device1 auth1 pwd1 '{"temp":20.0}'
