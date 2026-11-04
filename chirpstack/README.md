# ChirpStack

[ChirpStack](https://www.chirpstack.io/) is an open-source LoRaWAN® Network Server. It manages
gateways, devices and applications, handles the LoRaWAN MAC layer and security, and delivers
device data to your own systems, for example over MQTT to Home Assistant.

## Features

- Web UI and API for managing tenants, gateways, device profiles, applications and devices.
- Self-contained: uses SQLite for persistent storage and a bundled Valkey (Redis-compatible)
  instance for caching, so no external database is required.
- Multiple LoRaWAN regions can be enabled at once.
- MQTT gateway backend and integration, using the Home Assistant MQTT broker out of the box.
- Optional OpenID Connect login.
- Data lives in the app's `/data` directory and is included in Home Assistant backups.

## Getting started

1. Install and start the app, and make sure an MQTT broker (e.g. Mosquitto) is available.
2. Select your LoRaWAN `regions` in the app configuration.
3. Open the web UI and log in with `admin` / `admin`. Change the password immediately.
4. Point your gateway's packet forwarder (e.g. ChirpStack MQTT Forwarder) at the same MQTT broker,
   using the topic prefix of your region.

The web UI is not published on a host port by default; see [DOCS.md](./DOCS.md) for configuration,
network exposure and reverse proxy examples.
