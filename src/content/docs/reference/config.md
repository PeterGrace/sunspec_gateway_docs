---
title: Configuration File
description: Details about the configuration file
---
Example config.yaml

```yaml
---
hass_enabled: true
mqtt_server_addr: "10.174.2.40"
mqtt_server_port: 1883
mqtt_username: "pwrcell_rs"
mqtt_password: "pwrcell_rs"
units:
  - addr: "127.0.0.1:5083"
    slaves: [1,3, 4, 5, 6, 8, 9, 10, 11]
models:
  "102":
    - point: "PhVphA"
      interval: 15
      device_class: "voltage"
      state_class: "measurement"
      precision: 1
      value_min: 0.0
      value_max: 175
```


#### Root of config
| field | type | description |
| ----- | ---- | ----------- |
| .hass_enabled | bool | Optional value to turn off homeassistant config payloads, good for if you want to test this software without updating your existing home assistant yet.  Defaults to true.|
| .mqtt_server_addr | string | ip address for mqtt server |
| .mqtt_server_port | integer | port number for mqtt server, normally 1883 |
| .mqtt_username | String | username with access to interact with MQTT server |
| .mqtt_password | String | password for mqtt user |
| .units[] | List[UnitConfig] | List of units |
| .models{} | Map | Map of models and points to query |


#### UnitConfig 
| field | type | description |
| ----- | ---- | ----------- |
| .unit[].addr | String | IP and port of ModBus target, example "192.168.1.1:502" |
| .unit[].slaves | List[int] | Array of integers indicating which unit ids to query on this address |


#### ModelConfig

The model map consists of named models where the name field is the string literal of the model id, in other words, to query the W point in model 102, you'd indicate:
```yaml
models:
  "102": 
    - point: "W"
      interval: 10
```

| field | type | description |
| ----- | ---- | ----------- |
| .point | String | name of sunspec-defined point |
| .catalog_ref | String | for json-models with nested subgroups, this is a JMES-looking path to the data point, e.g. ".lithium_ion_string.lithium_ion_string_module[1].ModSoH" |
| .interval | integer | the interval, in seconds, to check this value.  For values that don't change very often, set this to a higher value to reduce strain on your ModBus connection.  This value will default to 10 seconds if a number lower than 10 is passed. |
| .device_class | String | If using HomeAssistant, this is the class of measurement.  Example: "energy" for SensorDeviceClass.ENERGY.  See [device classes](https://developers.home-assistant.io/docs/core/entity/sensor/#available-device-classes) for more info. |
| .state_class | String | If using HomeAssistant, this value is to define the type of state from this value. "measurement" is for gauge values, "total_increasing" for counters. |
| .uom | String | If using HomeAssistant, A custom unit of measurement to show in the sensor data. |
| .precision | integer | If using HomeAssistant, set the number of decimal places to show.  Does not affect math precision in the app. |
| .readwrite | bool | If using HomeAssistant, setting readwrite will allow you to change the value of the point in HomeAssistant.  That change will then be sent to the sunspec device.  For example, you can use this to change your device's operating mode, or disable devices remotely. |
| .homeassistant | bool | Enable/Disable HomeAssistant discovery.  Defaults to enabled, but if you want to gather data on MQTT but not necessarily have HomeAssistant ingest that data, set this value to false. |
| .scale_factor | integer | If for some reason the scale factor in your model is wrong and the data is coming back incorrect, you can override scale factor here.  | 
| .inputs | InputType | Configuration on what inputs to allow/expect for this read/write data point
| .input_only | bool | Set whether this point should only be used for sending inputs and never gathered. |
| .value_min | float | The minimum value to allow for this data point.  Useful if your device occasionally spews gibberish on the ModBus. |
| .value_max | float | The maximum value to allow for this data point.  Useful if your device occasionally spews gibberish on the ModBus. |
| .check_deviations | int | The maximum amount of value skew you will allow for this point, in standard deviations.  Useful if your device occasionally spews gibberish on the ModBus. |

#### InputConfig

The following input options are allowed:

##### Select
A select input is a list of values that the user can select through a drop-down.
      
```yaml
models:
  "64200":
    - point: "SysMd"
      interval: 60
      readwrite: true
      inputs:
        select:
          - "GRID_CONNECT"
          - "SELF_SUPPLY"
          - "CLEAN_BACKUP"
          - "PRIORITY_BACKUP"
          - "SAFETY_SHUTDOWN"
          - "SELL"
```

##### Switch
| field | type | description |
| ----- | ---- | ----------- |
| .on | String | string value to send on "ON" click |
| .off | String | string value to send on "OFF" click |

```yaml
models:
  "804":
    - point: "SetEna"
      interval: 30
      readwrite: true
      inputs:
        switch:
          on: ENABLED
          off: DISABLED
```

##### Button
The button is a single value which, when clicked in Home Assistant, sends the value specified here to the SunSpec device.
```yaml
models:
  "64263":
    - point: "ClearPVRSSLockoutError"
      interval: 300
      readwrite: true
      display_name: "Clear PVRSS Lock-Out"
      inputs:
        button: "CLEAR_ERROR"
```
##### Number
| field | type | description |
| ----- | ---- | ----------- |
| .min | Number | minimum value app will accept  |
| .max | Number | max value app will accept |


```yaml
  "802":
    - point: "SoCMin"
      interval: 60
      readwrite: true
      inputs:
        number:
          min: 0
          max: 100
      uom: "%"

```