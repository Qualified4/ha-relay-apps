# SmartThings LAN Relay

[한국어](README.md) · English

Connect SmartThings device states and vehicle/water mat controls to Home Assistant using a custom Edge driver, SmartThings Rules and local MQTT. Runtime does not store SmartThings OAuth credentials or poll its REST API. This project does not use AEB.

## Features

- Washer, vehicle and Navien water mat state discovery, including structured attributes
- Command buttons and catalog-defined MQTT climate/lock controls
- Local HVAC preferences; editing preferences does not actuate the vehicle
- Durable latest-state cache, reconnect replay and duplicate-request suppression
- Prebuilt `aarch64` and `amd64` images

## Setup

Home Assistant OS/Supervisor, an Edge hub, Mosquitto and the HA MQTT integration are required. **Installing this App alone is not enough.** A maintainer must configure the custom Edge driver, Rules and a private catalog using the private source repository. Arbitrary SmartThings devices are not automatically supported.

See [documentation](DOCS.en.md) for configuration, outage behavior and troubleshooting.

Cloud appliances still require their upstream services and internet access. Relay availability does not prove appliance freshness. Intermediate transitions may collapse during outages; uncertain commands are never automatically retried.

Use the relay port on the LAN only. App backups contain the permanent authentication key and cached device state. Keep them private. Git source is private, but code in the public image is extractable.
