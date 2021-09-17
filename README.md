# Pi-Hole + Unbound in just 1 Container

## Description

This is a fork of [Chris Crowe's docker-pihole-unbound Docker deployment](https://github.com/chriscrowe/docker-pihole-unbound) that runs both Pi-Hole and Unbound in a single container. Some changes in this fork include:
- Refactoring project to a single container, removing legacy two-container option
- Enabling of Pi-Hole and Unbound's IPv6 features by default
- Always grabbing the latest Pi-Hole Docker image
- Downloading root.hints file for Unbound
- Default port opening for Unbound for testing (port 5335)

The base image for the container is the [official Pi-Hole container](https://hub.docker.com/r/pihole/pihole), with an extra build step added to install the Unbound resolver directly into to the container based on [instructions provided directly by the Pi-Hole team](https://docs.pi-hole.net/guides/unbound/).

## Usage

First create a `.env` file to substitute variables for your deployment.


### Environment variables

> Vars and descriptions replicated from the [official Pi-Hole container](https://github.com/pi-hole/docker-pi-hole/):

| Docker Environment Var | Description|
| --- | --- |
| `ServerIP: <Host's IPv4>`<br/> | **--net=host mode requires** Set to your server's LAN IP, used by web block modes and lighttpd bind address.
| `ServerIPv6: <Host's IPv6>`<br/> | **\*Not required if manually commenting out IPv6 functionality in docker-compose.yaml\*** Set to your server's LAN IPv6 for Unbound.
| `TZ: <Timezone>`<br/> | Set your [timezone](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) to make sure logs rotate at local midnight instead of at UTC midnight.
| `WEBPASSWORD: <Admin password>`<br/> | http://pi.hole/admin password. Run `docker logs pihole \| grep random` to find your random pass.
| `REV_SERVER: <"true"\|"false">`<br/> | Enable DNS conditional forwarding for device name resolution
| `REV_SERVER_DOMAIN: <Network Domain>`<br/> | If conditional forwarding is enabled, set the domain of the local network router
| `REV_SERVER_TARGET: <Router's IP>`<br/> | If conditional forwarding is enabled, set the IP of the local network router
| `REV_SERVER_CIDR: <Reverse DNS>`<br/>| If conditional forwarding is enabled, set the reverse DNS zone (e.g. `192.168.1.0/24`)

Example `.env` file in the same directory as your `docker-compose.yaml` file:

```
ServerIP=192.168.1.2
ServerIPv6=fe80::
TZ=America/New_York
WEBPASSWORD={desired password here}
REV_SERVER=true
REV_SERVER_DOMAIN=local
REV_SERVER_TARGET=192.168.1.1
REV_SERVER_CIDR=192.168.1.0/24
HOSTNAME=pihole
DOMAIN_NAME=pihole.local
```

### Using Portainer stacks?

Portainer stacks are a little weird and don't want you to declare your named volumes, so remove this block from the top of the `docker-compose.yaml` file before copy/pasting into Portainer's stack editor:

```yaml
volumes:
  etc_pihole-unbound:
  etc_pihole_dnsmasq-unbound:
```

### Running the stack

```bash
docker-compose up -d
```

> If using Portainer, just paste the `docker-compose.yaml` contents into the stack config and add your *environment variables* directly in the UI.
