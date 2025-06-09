---
title: Quick-Start
description: How to get started with sunspec_gateway
---

### Prerequisites

#### Docker / Docker Compose
sunspec_gateway is designed to run inside a docker container.  Because of this, you will need the ability to run a container image.

#### A SunSpec-compatible device
The equipment you are monitoring must be SunSpec-compliant and able to be queried.

### Get Started

#### Set-Up
This docker-compose.yml file will start the app:

```yaml
services:
  sunspec_gateway:
    image: docker.io/petergrace/sunspec_gateway:latest
    volumes:
      - "./config.yaml:/opt/sunspec_gateway/config.yaml"
```

Before you start, however, you'll need to have a config.yaml file in the same directory as the docker-compose file.  Here's a very basic config file:
```yaml
---
mqtt_server_addr: "192.168.1.40"
mqtt_server_port: 1883
mqtt_username: "ha_username"
mqtt_password: "ha_password"
units:
  - addr: "192.168.1.10:502"
    slaves: [1,3]
models:
  "1":
    - point: "Mn"
      interval: 30
```
In the above example, you'd want to change the following values accordingly:

  * mqtt_server_addr: The ip address of your mqtt server.  If you're using HomeAssistant, this will likely be your internal HomeAssistant ip address.
  * mqtt_server_port: If you're using a different port for mqtt, specify it here. 1883 is the default for MQTT, and is what HomeAssistant uses.
  * mqtt_username: A username that has access to login to mqtt.  In HomeAssistant, if you're using the mosquitto add-on, you set this up under People->Users.
  * mqtt_password: password for the aforementioned user.
  * units: this is a list of sunspec-compatible devices you wish to query.  If you only have one inverter, then you'd just put the info in for one unit.
    * addr: ip:port of the sunspec-compatible ModBus-tcp server you're connecting to
    * slaves: In the ModBus specification, the unit you connect to is considered a "master" and the devices you query are called "slaves."  Some units have multiple devices that can be queried on the same connection, so specify those unit ids here.
  * models: this section is where you can specify which data points you'd like to query.  In our example here, we're only querying for a point that we know all sunspec devices contain.  This will be enough to start your journey, but there are a lot of datapoints available!

  For more information on the config file format, have a look at our [Config Reference](/reference/config)



## Further reading

- Read [about how-to guides](https://diataxis.fr/how-to-guides/) in the Diátaxis framework
