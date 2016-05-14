---
layout: page
title: "RESTful Sensor"
description: "Instructions how to integrate REST sensors into Home Assistant."
date: 2015-09-14 19:10
sidebar: true
comments: false
sharing: true
footer: true
ha_category: Sensor
ha_release: 0.7.4
---


The `rest` sensor platform is consuming a given endpoint which is exposed by a [RESTful API](https://en.wikipedia.org/wiki/Representational_state_transfer) of a device, an application, or a web service. The sensor has support for GET and POST requests.

To enable this sensor, add the following lines to your `configuration.yaml` file for a GET request:

```yaml
# Example configuration.yaml entry
sensor:
  platform: rest
  resource: http://IP_ADDRESS/ENDPOINT
  value_template: '{% raw %}{{ value_json.thermostat }}{% endraw %}'
  method: GET
  name: REST GET sensor
  unit_of_measurement: "°C"
```

or for a POST request:

```yaml
# Example configuration.yaml entry
sensor:
  platform: rest
  resource: http://IP_ADDRESS/ENDPOINT
  method: POST
  value_template: '{% raw %}{{ value_json.thermostat }}{% endraw %}'
  payload: '{ "device" : "heater" }'
  name: REST POST sensor
  unit_of_measurement: "°C"
```

Configuration variables:

- **resource** (*Required*): The resource or endpoint that contains the value.
- **method** (*Optional*): The method of the request. Default is GET.
- **value_template** (*Optional*): Defines a [template](/topics/templating/) to extract the value.
- **payload** (*Optional*): The payload to send with a POST request. Depends on the service, but usually formed as JSON.
- **name** (*Optional*): Name of the REST sensor.
- **unit_of_measurement** (*Optional*): Defines the unit of measurement of the sensor, if any.

<p class='note warning'>
Make sure that the URL matches exactly your endpoint or resource.
</p>

`curl` could help you with the identification of the variable you want to display in your Home Assistant frontend. The example below show the JSON response of a device that is running with [aREST](http://arest.io/).

```bash
$ curl -X GET http://192.168.1.31/temperature/
{"temperature": 77, "id": "sensor02", "name": "livingroom", "connected": true}
```

## {% linkable_title Examples %}

In this section you find some real life examples of how to use this sensor.

### {% linkable_title External IP address %}

You can find your external IP address using the service [JSON Test](http://www.jsontest.com) at their http://ip.jsontest.com/ endpoint.

To display the IP address, the entry for a sensor in the `configuration.yaml` file will look like this.

```yaml
  - platform: rest
    resource: http://ip.jsontest.com
    name: External IP
    value_template: '{% raw %}{{ value_json.ip }}{% endraw %}'
```

### {% linkable_title Single value from a local Glances instance %}

The [glances](/components/sensor.glances/) sensor is doing the exact same thing for all exposed values.

Add something similar to the entry below to your `configuration.yaml` file:

```yaml
  - platform: rest
    resource: http://IP_ADRRESS:61208/api/2/mem/used
    name: Used mem
    value_template: '{% raw %}{{ value_json.used| multiply(0.000000954) | round(0) }}{% endraw %}'
    unit_of_measurement: MB
```

### {% linkable_title Value for other Home Assistant instance %}

The Home Assistant [API](/developers/rest_api/) exposes the data from your attached sensors. If you are running multiple Home Assistant instances which are not [connected](/developers/architecture/#multiple-connected-instances) you can still get information from them.


```yaml
  - platform: rest
    resource: http://IP_ADDRESS:8123/api/states/sensor.weather_temperature?api_password[PASSWORD]
    name: Temperature
    value_template: {% raw %}'{{ value_json.state }}'{% endraw %}
    unit_of_measurement: "°C"
```

