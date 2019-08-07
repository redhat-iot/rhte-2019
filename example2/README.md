# IoT weather application

**Note:** The IoT functionality of AMQ Online is still tech preview.

## Register a new device

    hat context create tutorial-iot https://device-registry-enmasse-infra.apps.wonderful.iot-playground.org --default-tenant rhte2019.iot
    hat reg create device1
    hat cred set-password auth1 sha-256 pwd1 --device device1

## Publish as device

    hot publish http telemetry https://iot-http-adapter-enmasse-infra.apps.wonderful.iot-playground.org rhte2019.iot device1 auth1 pwd1 '{"temp":20.0}' -t "application/json"

