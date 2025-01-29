# Trunk Recorder MQTT Status (and Units!) Plugin <!-- omit from toc -->

This is a plugin for Trunk Recorder that publish the current status over MQTT. External programs can use the MQTT messages to collect and display information on monitored systems.

Requires trunk-recorder 5.0 or later, and Paho MQTT libraries

- [Install](#install)
- [Configure](#configure)
- [MQTT Messages](#mqtt-messages)
- [Trunk Recorder States](#trunk-recorder-states)
- [MQTT Brokers](#mqtt-brokers)
  - [Mosquitto MQTT Broker](#mosquitto-mqtt-broker)
  - [NanoMQ](#nanomq)
- [Docker](#docker)

## Install

1. **Clone Trunk Recorder** source following these [instructions](https://github.com/robotastic/trunk-recorder/blob/master/docs/Install/INSTALL-LINUX.md).
   
2. **Install the Paho MQTT C & C++ Libraries**.

&emsp; If your package manager provides recent Paho MQTT libraries, e.g:

```bash
sudo apt install libpaho-mqtt-dev libpaho-mqttpp-dev
```

&emsp; If not, you may build and install these libraries from source:

&emsp; - _Install Paho MQTT C_

```bash
git clone https://github.com/eclipse/paho.mqtt.c.git
cd paho.mqtt.c

cmake -Bbuild -H. -DPAHO_ENABLE_TESTING=OFF -DPAHO_BUILD_STATIC=ON  -DPAHO_WITH_SSL=ON -DPAHO_HIGH_PERFORMANCE=ON
sudo cmake --build build/ --target install
sudo ldconfig
```

&emsp; - _Install Paho MQTT C++_

```bash
git clone https://github.com/eclipse/paho.mqtt.cpp
cd paho.mqtt.cpp

cmake -Bbuild -H. -DPAHO_BUILD_STATIC=ON
sudo cmake --build build/ --target install
sudo ldconfig
```

3. **Build and install the plugin:**

&emsp; This pluigin source should be cloned into the `/user_plugins` directory of the Trunk Recorder 5.0+ source tree.  It will be built and installed along with Trunk Recorder.

```bash
cd [your trunk-recorder github source directory]
cd user_plugins
git clone https://github.com/taclane/trunk-recorder-mqtt-status
cd [your trunk-recorder build directory]
sudo make install
```

&emsp; **NOTE:** Plugins will be automatically built and installed with Trunk Recorder.  To update either Trunk Recorder or a plugin, simply `cd` into the appropriate git directory and `git pull`.  Refer to the above instructions to `make install` any updates.

## Configure

**Plugin options:**

| Key             | Required | Default Value        | Type       | Description                                                                                                                                                                              |
| --------------- | :------: | -------------------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| broker          |    ✓     | tcp://localhost:1883 | string     | The URL for the MQTT Message Broker. It should include the protocol used: **tcp**, **ssl**, **ws**, **wss** and the port, which is generally 1883 for tcp, 8883 for ssl, and 443 for ws. |
| topic           |    ✓     |                      | string     | This is the base MQTT topic. The plugin will create subtopics for the different status messages.                                                                                         |
| unit_topic      |          |                      | string     | Optional topic to report unit stats over MQTT.                                                                                                                                           |
| message_topic   |          |                      | string     | Optional topic to report trunking messages over MQTT.                                                                                                                                    |
| console_logs    |          | false                | true/false | Optional setting to report console messages over MQTT.                                                                                                                                   |
| username        |          |                      | string     | If a username is required for the broker, add it here.                                                                                                                                   |
| password        |          |                      | string     | If a password is required for the broker, add it here.                                                                                                                                   |
| client_id       |          | tr-status-xxxxxxxx   | string     | Override the client_id generated for this connection to the MQTT broker.                                                                                                                 |
| mqtt_audio      |          | false                | true/false | Optional setting to report audio in base64 and call metadata over MQTT.                                                                                                                  |
| mqtt_audio_type |          | wav                  | string     | Control which audio files to emit.  `wav`, `m4a` (if compression enabled), `both`, `none` (only the .json)                                                                               |
| include_sys_info|          | false                | true/false | Include system identification fields (sysid, wacn, nac) in messages.                                                                                                                     |
| qos             |          | 0                    | int        | Set the MQTT message [QOS level](https://www.eclipse.org/paho/files/mqttdoc/MQTTClient/html/qos.html)                                                                                    |

**Trunk-Recorder options:**

| Key                          | Required | Default Value               | Type   | Description                                                                                |
| ---------------------------- | :------: | --------------------------- | ------ | ------------------------------------------------------------------------------------------ |
| [instanceId](./config.json) |          | <nobr>trunk-recorder</nobr> | string | Append an `instance_id` key to identify the trunk-recorder instance sending MQTT messages. |

**Plugin Usage:**

See the included [config.json](./config.json) for an example how to load this plugin.

```json
    "plugins": [
    {
        "name": "MQTT Status",
        "library": "libmqtt_status_plugin.so",
        "broker": "tcp://io.adafruit.com:1883",
        "topic": "robotastic/feeds",
        "unit_topic": "robotastic/units",
        "username": "robotastic",
        "password": "",
        "console_logs": true,
        "mqtt_audio": false,
        "include_sys_info": true,
        "mqtt_qos": 0
    }]
```

If the plugin cannot be found, or it is being run from a different location, it may be necessary to supply the full path:

```json
        "library": "/usr/local/lib/trunk-recorder/libmqtt_status_plugin.so",
```

[Rest of README content remains unchanged...]
