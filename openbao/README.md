# Home Assistant App: OpenBao

OpenBao is an open-source, community-driven secrets and encryption management
system that safely stores, generates, and protects sensitive data such as
passwords, API keys, and certificates.

This app runs OpenBao on the Home Assistant Ubuntu base image and includes
PC/SC Lite support for USB smart-card readers.

The SafeNet Authentication Client libraries are not bundled with this image
for licensing reasons. To use a SafeNet-backed token/HSM, place the SafeNet
`.deb` packages in `/share/openbao/deb` on the Home Assistant host; the app
installs any `.deb` files found there at startup. SafeNet only publishes
amd64 packages, so this only works on amd64 hosts even though the app itself
supports aarch64 and amd64.

![Supports aarch64 Architecture][aarch64-shield]
![Supports amd64 Architecture][amd64-shield]

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
