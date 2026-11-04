# https://developers.home-assistant.io/docs/apps/configuration#app-dockerfile

# Image reference is literal so Dependabot can track it
FROM ghcr.io/hassio-addons/debian-base:9.5.1

ARG CHIRPSTACK_VERSION=4.20.0

# Install Valkey from Debian and the official ChirpStack SQLite .deb package.
# The package's default config in /etc/chirpstack is used to seed the app config.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        valkey-server \
        valkey-tools \
        ca-certificates \
        tzdata \
    && curl -fsSL -o /tmp/chirpstack.deb \
        "https://artifacts.chirpstack.io/downloads/chirpstack/chirpstack-sqlite_${CHIRPSTACK_VERSION}_linux_$(dpkg --print-architecture).deb" \
    && dpkg -i /tmp/chirpstack.deb \
    && rm -f /tmp/chirpstack.deb \
    && mkdir -p /usr/share/chirpstack \
    && cp -r /etc/chirpstack /usr/share/chirpstack/configuration \
    && rm -rf /var/lib/apt/lists/*

# Copy root filesystem
COPY rootfs /

# Web UI and API
EXPOSE 8080

# Metadata Labels
LABEL org.opencontainers.image.title="Home Assistant App: ChirpStack" \
      org.opencontainers.image.description="ChirpStack LoRaWAN Network Server with SQLite and Valkey." \
      org.opencontainers.image.source="https://github.com/karolkozlowski/home-assistant-addons/tree/main/chirpstack" \
      org.opencontainers.image.licenses="MIT"
