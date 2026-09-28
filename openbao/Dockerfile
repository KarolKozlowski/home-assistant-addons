# https://developers.home-assistant.io/docs/apps/configuration#app-dockerfile
ARG BUILD_FROM=ghcr.io/home-assistant/base-ubuntu:24.04

# Define the base image for the OpenBao binaries
ARG OPENBAO_IMAGE=ghcr.io/openbao/openbao:2

# Binary source stages
FROM --platform=linux/amd64 ${OPENBAO_IMAGE} AS openbao-amd64
FROM --platform=linux/arm64 ${OPENBAO_IMAGE} AS openbao-arm64

# Dynamically pick the source stage based on TARGETARCH
ARG TARGETARCH
FROM openbao-${TARGETARCH} AS openbao-bin

FROM ${BUILD_FROM}

# Execute during the build of the image
ARG TEMPIO_VERSION=2021.09.0
ARG TARGETARCH
RUN \
    if [ -z "${TARGETARCH}" ]; then \
        echo "TARGETARCH is not set, please use Docker BuildKit for the build." && exit 1; \
    fi \
    && case "${TARGETARCH}" in \
        amd64) tempio_arch="amd64" ;; \
        arm64) tempio_arch="aarch64" ;; \
        *) echo "Unsupported TARGETARCH: ${TARGETARCH}" && exit 1 ;; \
    esac \
    && curl -sSLf -o /usr/bin/tempio \
        "https://github.com/home-assistant/tempio/releases/download/${TEMPIO_VERSION}/tempio_${tempio_arch}"

# Create a non-root user to run the software and install runtime dependencies.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        pcscd \
        libpcsclite1 \
        libccid \
        opensc \
        pcsc-tools \
        ca-certificates \
        dumb-init \
        gosu \
        libcap2 \
        tzdata \
    && groupadd --system openbao \
    && useradd --system --gid openbao --no-create-home --shell /usr/sbin/nologin openbao \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /openbao/logs && \
    mkdir -p /openbao/file && \
    mkdir -p /openbao/config && \
    chown -R openbao:openbao /openbao

# Copy root filesystem
COPY rootfs /

# Copy the OpenBao binaries from the source image
COPY --from=openbao-bin /usr/bin/bao /usr/bin/bao
RUN ln -s /usr/bin/bao /usr/bin/vault

# 8200/tcp is the primary interface that applications use to interact with
# OpenBao.
EXPOSE 8200


# Metadata Labels
LABEL openbao.skip="true" \
      org.opencontainers.image.title="Home Assistant App: OpenBao" \
      org.opencontainers.image.description="OpenBAO app for Home Assistant." \
      org.opencontainers.image.source="https://github.com/karolkozlowski/home-assistant-addons/tree/main/openbao" \
      org.opencontainers.image.licenses="Apache License 2.0"
