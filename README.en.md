# SmartThings LAN Relay Apps

[한국어](README.md) · English

Connect SmartThings devices to Home Assistant using a custom Edge driver, Rules, and local MQTT. This repository provides installation metadata and documentation; source is maintained privately.

Add `https://github.com/Qualified4/ha-relay-apps` under **Settings → Apps → App Store → Repositories**. Supports `aarch64` and `amd64` with public prebuilt images; no GitHub token is required.

**Installing the App alone is not enough:** the custom Edge driver, device-specific Rules, a private catalog, Mosquitto and the HA MQTT integration are required. Setup tools are in the private source repository. Not every SmartThings device is automatically supported.

[App introduction](smartthings_relay/README.en.md) · [Documentation](smartthings_relay/DOCS.en.md)

Keep personal addresses, identifiers, keys and state in local configuration. Code inside the public image is extractable.
