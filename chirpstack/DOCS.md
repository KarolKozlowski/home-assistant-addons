# ChirpStack

## Configuration

The ChirpStack configuration is generated from the options below on every start.

| Option | Description |
|--------|-------------|
| `log_level` | `error`, `warn`, `info` (default), `debug` or `trace`. |
| `regions` | Regions to enable (default `eu868`). Multiple regions can be enabled at once. |
| `net_id` | Six hex digits, default `000000`. |
| `api_secret` | API token secret. Generated and stored in `/data` when empty. |
| `allow_unknown_gateways` | Accept frames from gateways not registered in ChirpStack. |
| `mqtt_server`, `mqtt_username`, `mqtt_password` | MQTT broker, e.g. `tcp://192.168.1.10:1883/`. If `mqtt_server` is empty, the Home Assistant MQTT service (Mosquitto) is used. Applies to the integration and to the gateway backend of every region. |
| `mqtt_json` | Publish events as JSON (default) instead of Protobuf. |
| `authentication` | `internal` (default) or `openid_connect`. |
| `oidc_*` | OpenID Connect settings, used when `authentication` is `openid_connect`. |
| `custom_config` | Use `chirpstack.toml` from the app config folder instead (see below). |

## Network exposure

Port 8080 (web UI and API) is not published by default. Other apps on the Home Assistant
network, such as a reverse proxy, can reach it at `http://<hostname>:8080`, where the hostname
is shown on the app's Info page (for local apps it is `local-chirpstack`; for apps installed
from a repository it is a hash prefix such as `98b95307-chirpstack`). Set a host port in the
app's **Network** section to publish it instead. Gateways connect over MQTT, so no other port is needed.

### Exposing through the NGINX app

Example drop-in configuration for the NGINX Home Assistant proxy app, e.g.
`/share/nginx_proxy/chirpstack.conf`. Replace the server name, certificate paths and the
`proxy_pass` hostname with your own. The UI is served from the site root, so use a dedicated
(sub)domain rather than a path prefix.

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name chirpstack.example.com;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    listen [::]:443 ssl;

    server_name chirpstack.example.com;

    # Use the actual filenames configured in your NGINX app.
    # The certificate must include chirpstack.example.com.
    ssl_certificate     /ssl/fullchain.pem;
    ssl_certificate_key /ssl/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;

    location / {
        proxy_pass http://98b95307-chirpstack:8080;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP $remote_addr;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;

        proxy_buffering off;
        proxy_read_timeout 3600s;
    }
}
```

`$connection_upgrade` is defined by the NGINX app's main configuration. If your setup does not
define it, add `map $http_upgrade $connection_upgrade { default upgrade; "" close; }` at the
`http` level. When using OpenID Connect, set `oidc_redirect_url` to
`https://chirpstack.example.com/auth/oidc/callback`.

## OpenID Connect

Set `authentication` to `openid_connect` and fill in `oidc_provider_url`, `oidc_client_id`,
`oidc_client_secret` and `oidc_redirect_url` (`https://<your-chirpstack-url>/auth/oidc/callback`).
Generic OAuth2 providers are not exposed as options; use `custom_config` for those.

## Regions

All `region_*.toml` files are copied to the app config folder (`/addon_configs/<slug>_chirpstack`)
on first start. Select which ones are active with `regions`; tune channels and other region
parameters by editing the files. Files are never overwritten, and the MQTT server and credentials
in untouched files follow the app options.

## Custom configuration

With `custom_config` enabled, ChirpStack runs from `chirpstack.toml` in the app config folder.
The file is created from the current options on first start and never touched afterwards, so
all `regions`, `mqtt_*`, `oidc_*` and similar options are then ignored (except that `$MQTT_SERVER`,
`$MQTT_USERNAME`, `$MQTT_PASSWORD`, `$LOG_LEVEL` and `$CHIRPSTACK_API_SECRET` placeholders can be used in it).
The Valkey address is `redis://127.0.0.1:6379/` and the SQLite path is `sqlite:///data/chirpstack.sqlite`.
