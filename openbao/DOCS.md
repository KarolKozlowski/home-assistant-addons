# OpenBao

OpenBao is an open-source secrets manager for storing and serving secrets,
encryption keys, and certificates.

## Configuration

Create an OpenBao HCL configuration file in the Home Assistant app
configuration directory. The default filename is `bao.hcl`; change
**Config file** only when using another filename.

The file is mounted into the app at `/config`. OpenBao persistent data is
stored under `/data`, including the Raft database at `/data/bao` and log or
file-storage directories. The app creates these directories and assigns them
to the `openbao` service user at startup.

For a TCP listener, bind OpenBao to port `8200`, which is exposed by this app.
If the listener uses TLS, place the certificate and key in Home Assistant's
`/ssl` directory and reference them using paths under `/ssl` in `bao.hcl`.

The app exposes USB devices to OpenBao. This is useful when a configured seal,
authentication method, or plugin requires USB hardware. Device access is
available inside the container under `/dev`.

The app also starts `pcscd` under supervision and includes CCID and PC/SC
driver support for USB smart-card readers. Readers should be connected to the
Home Assistant host before starting or restarting the app.

The SafeNet Authentication Client libraries are not bundled with this image
for licensing reasons. To use a SafeNet-backed token/HSM, place the SafeNet
`.deb` packages in `/share/openbao/deb` on the Home Assistant host; the app
installs any `.deb` files found there at startup. SafeNet only publishes
amd64 packages, so this only works on amd64 hosts even though the app itself
supports aarch64 and amd64.

This app was tested with [Linux_SAC_10.9_GA.zip](https://www.digicert.com/StaticFiles/Linux_SAC_10.9_GA.zip),
obtained by following DigiCert's
[SafeNet Authentication Client download instructions](https://knowledge.digicert.com/general-information/how-to-download-safenet-authentication-client).

## Start the app

1. Create `/config/bao.hcl` in the app configuration directory.
2. Verify that storage paths are writable locations under `/data`.
3. If TLS is enabled, place the required certificate files under `/ssl` and
   reference them from the configuration.
4. Connect the USB smart-card reader if your configuration requires one.
5. Start the app and review the logs for configuration, storage, or PC/SC
  errors.

OpenBao runs as the unprivileged `openbao` user after startup. The app prepares
the persistent storage directories before dropping privileges.

## Troubleshooting

- A missing configuration message means the configured filename does not exist
  under the Home Assistant app configuration directory.
- A Raft or Bolt permission error usually means the storage path is outside
  `/data`, or the app needs to be restarted after a storage-path change.
- For TLS errors, check that the certificate and key paths point to `/ssl` and
  that the files are readable by the `openbao` user.
- For smart-card errors, check that the reader is visible on the Home Assistant
  host, the reader uses a supported CCID driver, and the logs contain a
  successful `pcscd` startup message.
- The `disable_mlock` setting is not supported by some OpenBao versions. Remove
  it from `bao.hcl` if OpenBao reports it as an unknown field.

See the [OpenBao documentation](https://openbao.org/docs/) for configuration,
storage, sealing, authentication, and listener options.
