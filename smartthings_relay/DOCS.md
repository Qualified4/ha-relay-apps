# SmartThings LAN Relay

Home Assistant App: SmartThings Rules forward cloud-device attributes to an Edge driver,
which sends authenticated LAN updates to this App. HA discovers sensors and vehicle
controls through its existing local MQTT broker. Runtime does not use a SmartThings
OAuth token or poll the SmartThings REST API.

## Install

Use the public App repository https://github.com/Qualified4/ha-relay-apps .
The image supports Home Assistant Green (aarch64) and x86-64 HAOS (amd64).
An installed, configured Mosquitto App and HA MQTT integration are required.

Configure the hub LAN IPv4 and relay device ID in the App Configuration tab.
A persistent random relay key is generated on first start and masked in Configuration.
It has no automatic expiry. Copy it to the Edge device's authentication preference.
Rotate it by setting a new 32–128 character key in both places and restarting the App.
App backups include its key and private status cache; keep backups private.

Install edge-driver/ on your SmartThings Edge hub, then set the receiver IP to HA's LAN
address and port 18088. The listener registers its dynamic hub-side port automatically.
Do not expose port 18088 to the internet. TLS proxying is optional for other services;
this direct LAN protocol does not require NPM.

## Provisioning

Use tools/setup.py with the SmartThings CLI to generate a private catalog and Rules.
The input contains location, relay, and washer/vehicle device IDs; write input/output
outside this repository. Run --help for the format. Without --apply it only generates
files. --apply creates status and vehicle-control Rules and records their IDs.
Do not run --apply twice: use the installation manifest to manage exactly these Rules.

POST catalog.json to /configure with X-Relay-Key. Execute the recorded snapshot Rules
once to initialize current status; snapshot Rules only read source attributes.
Afterwards, explicit attribute-event Rules send subsequent updates automatically.
Rules use Always triggers and compare event timestamps, grouped by capability,
so provider event flags and complex attribute value types do not suppress updates. Large metadata
(over 1KB, such as the washer's 46KB supported-cycle list) also receives a one-minute
Rule-based refresh: automatic large-object change triggering is not reliable. This
refresh uses Rules, not a continuously authenticated REST client.
All source values, including nested objects/arrays, are available under sensor attributes.
Long/structured values use a short display state to respect HA's 255-character limit.
Null source attributes remain unknown. Container type hints preserve singleton arrays and
empty objects when intermediate platform/Lua serialization changes their representation. A cached value does not prove appliance freshness.

Vehicle commands without arguments appear as HA buttons. Parameterized commands use
the Vehicle command JSON text entity (up to 255 characters) or mqtt.publish on
smartthings_lan_relay/command, with retain:false and {"route":"catalog route","args":[...]}.
For example use the catalog's vehicleHvacRemoteSwitch/on route with the supported HVAC
argument object. Every argument is checked against SmartThings capability schemas.
All 11 exposed vehicle commands are provisioned, including HVAC, engine, door locks,
refresh, and finder commands. A command supported in a profile may still be rejected by
the upstream vehicle service; engine start on an ICE vehicle can require HVAC instead.

## Delivery and outages

Status: latest value per key is durable on the App; the Edge driver caches latest values
and periodically replays them. During downtime intermediate transitions may be collapsed.
This is eventual latest-state synchronization, not an audit log.
App/HA restarts replay cached values. MQTT broker reconnection republishes discovery
and states. Edge heartbeat loss marks entities unavailable after about 90 seconds;
availability indicates the LAN relay link, not SmartThings-cloud or appliance health.
Use source health/status attributes and timestamps to judge stale cloud data.

Control: HA → local MQTT → App → authenticated Edge listener → request attribute →
SmartThings Rule → real device/cloud service. Edge acknowledgement only means the
request was accepted by the driver. Confirm completion through returned device states.
A timeout is uncertain, and is never retried automatically. Recent 256 request IDs are
remembered on both sides. Retained MQTT controls are ignored; explicitly supply stable
request_id values when an external caller needs duplicate suppression.

HA/App/LAN outage prevents commands. Internet/SmartThings outage also prevents control
of cloud appliances and their status updates, even when the Edge/LAN connection works.
The App contains no SmartThings cloud credentials at runtime; one-time CLI provisioning
still uses the account's normal authentication and platform rules/quotas apply.

## Privacy and build

Private source → Actions multiarch build → public GHCR image.
Public metadata contains no personal device IDs, IP addresses, statuses or tokens.
Only relay Python code and dependency lock pins enter the image, enforced by an
allowlist .dockerignore. Runtime secrets and catalogs live only under /data.
Container contents are extractable: public image does not hide compiled/source code.

Validation: python -m unittest discover -s tests -v

Native MQTT controls can be stored in the private runtime catalog under entities.
Optional HVAC settings are persisted locally. Changing temperature, duration, or defog
preferences never actuates the vehicle; only an explicit HVAC on/mode command uses them.
