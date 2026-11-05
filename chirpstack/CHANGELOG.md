<!-- markdownlint-disable MD041 -->
<!-- https://developers.home-assistant.io/docs/apps/presentation#keeping-a-changelog -->
## 4.20.0-3

- Bump hassio-addons/debian-base from 9.5.1 to 9.5.2

## 4.20.0-2

- Add optional import of chirpstack-device-profiles from /share/chirpstack/device-profiles

## 4.20.0-1

- Initial release of ChirpStack 4.20.0 (SQLite build) with bundled Valkey
- Configuration generated from app options: regions, NetID, API secret, unknown gateways, MQTT
- MQTT broker from the Home Assistant MQTT service or configured manually
- Optional OpenID Connect authentication
- `custom_config` option to run from a user-managed chirpstack.toml
- Web UI port 8080 is not published by default
