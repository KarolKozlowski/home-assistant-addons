# https://developers.home-assistant.io/docs/apps/configuration#app-dockerfile
ARG BUILD_FROM=ghcr.io/home-assistant/base-debian:bookworm

# Define the base image for the Periphery binaries
ARG BINARIES_IMAGE=ghcr.io/moghtech/komodo-binaries:2
ARG X86_64_BINARIES=${BINARIES_IMAGE}-x86_64
ARG AARCH64_BINARIES=${BINARIES_IMAGE}-aarch64

# Binary source stages
FROM --platform=linux/amd64 ${X86_64_BINARIES} AS periphery-amd64
FROM --platform=linux/arm64 ${AARCH64_BINARIES} AS periphery-arm64

# Dynamically pick the source stage based on TARGETARCH
ARG TARGETARCH
FROM periphery-${TARGETARCH} AS periphery-bin

# Use the base image for the build
FROM ${BUILD_FROM}

ARG TARGETARCH
ARG TEMPIO_VERSION=2021.09.0

# Dependencies for Debian base
ARG docker_cli="docker.io docker-buildx-plugin docker-compose-plugin"
ARG generic_tools="git curl wget ca-certificates"

# Install official Docker APT repository and Docker CLI tools
RUN install -m 0755 -d /etc/apt/keyrings \
    && curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc \
    && chmod a+r /etc/apt/keyrings/docker.asc \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" > /etc/apt/sources.list.d/docker.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        ${docker_cli} \
        ${generic_tools} \
    && rm -rf /var/lib/apt/lists/*

# Download tempio
RUN tempio_arch=$(echo "${TARGETARCH}" | sed 's/arm64/aarch64/') \
    && curl -sSLf -o /usr/bin/tempio \
        "https://github.com/home-assistant/tempio/releases/download/${TEMPIO_VERSION}/tempio_${tempio_arch}" \
    && chmod +x /usr/bin/tempio

# Starship prompt
RUN curl -sS https://starship.rs/install.sh | sh -s -- --yes --bin-dir /usr/local/bin

# Copy binary from the selected architecture stage
COPY --from=periphery-bin /periphery /usr/local/bin/periphery

# Copy root filesystem
COPY rootfs /

# Metadata Labels
LABEL komodo.skip="true" \
      org.opencontainers.image.title="Home Assistant App: Komodo Periphery" \
      org.opencontainers.image.description="Komodo Periphery app for Home Assistant." \
      org.opencontainers.image.source="https://github.com/karolkozlowski/home-assistant-addons/tree/main/komodo-periphery" \
      org.opencontainers.image.licenses="Apache License 2.0"
