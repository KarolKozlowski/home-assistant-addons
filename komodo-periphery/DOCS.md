# Komodo Periphery

Komodo Periphery is the agent that allows [Komodo Core](https://komo.do/) to
manage this Home Assistant host. It connects to Komodo Core over a WebSocket
and provides access to Docker and the configured Periphery working directory.

This app has access to the Docker API. Only install it on a host that you
intend to manage from Komodo.

## Before you start

1. Install and start Komodo Core.
2. In Komodo, open **Servers**, create the server you want Periphery to use,
    and create an onboarding key in the server settings. Copy the key; Komodo
     shows it only when it is created.
3. Install this app in Home Assistant.

## Configuration

Configure the app before starting it:

- **Core address**: The address of Komodo Core reachable from the app, for
    example `komodo.example.com`.
- **Connect as**: The name this periphery instance as it should appear to
    Komodo Core.
- **Onboarding key**: Paste the key created in Komodo. It is required when
    connecting a new server. It is not needed for a server that has already
    been connected and authenticated.
- **Root directory**: The persistent directory used by Periphery for managed
    repositories, stacks, builds, and its key material. The default is
    `/data/komodo`.
- **Disable terminals**: Disable remote terminal access through Periphery.
- **Disable container exec**: Disable remote shell access to containers.
- **Include disk mounts** and **Exclude disk mounts**: Optional comma-separated
    paths used to filter the disk mounts reported to Komodo. Usually leave both
    empty unless disk usage is being reported incorrectly.

Start the app after saving the configuration. On the first successful
connection, Periphery creates its key pair and Komodo records the public key.
The onboarding key can then be removed from the app configuration if the
server is already registered in Komodo.

## Verify the connection

Open the app logs and confirm that Periphery starts without an error. Then
open the server in Komodo and confirm that its connection status is healthy.
The app must be able to reach the Core address, and Core must be able to
authenticate the Periphery instance.

## Troubleshooting

- If the app cannot connect, verify that **Core address** includes the correct
    hostname, port, and protocol and is reachable from Home Assistant.
- If onboarding fails, create a new onboarding key and restart the app. Treat
    onboarding keys as secrets and do not share them.
- If Docker operations fail, verify that the Home Assistant host's Docker API
    is available and that the paths used by your stacks are accessible to the
    Docker engine.

For the complete Komodo connection and authentication reference, see
[Connect More Servers](https://komo.do/docs/setup/connect-servers).
