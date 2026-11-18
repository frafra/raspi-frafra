# Zigbee2MQTT

[Zigbee2MQTT](https://www.zigbee2mqtt.io/) permette di usare il [RaspBee II](../hardware/raspbee2.md) con numerosi dispositivi Zigbee (come le prese e i bottoni IKEA Trådfri) tramite MQTT.

Servono il [RaspBee II configurato](../hardware/raspbee2.md), [Mosquitto](mqtt.md) attivo e Podman, installato come descritto in [Requisiti](../requisiti.md).

## Configurazione

```bash
mkdir -p ~/Code/zigbee2mqtt
```

`~/Code/zigbee2mqtt/configuration.yaml`:

```yaml
homeassistant:
  enabled: true
frontend:
  enabled: true
mqtt:
  base_topic: zigbee2mqtt
  server: mqtt://127.0.0.1
serial:
  port: /dev/ttyAMA0
  adapter: deconz
  baudrate: 38400
version: 5
```

## Servizio

`~/.config/containers/systemd/zigbee2mqtt.container`:

```ini
[Unit]
Description=Zigbee2MQTT
Requires=mosquitto.service
After=mosquitto.service

[Container]
ContainerName=zigbee2mqtt
Image=registry.hub.docker.com/koenkk/zigbee2mqtt:latest
Network=host
Volume=%h/Code/zigbee2mqtt:/app/data
Volume=/run/udev:/run/udev:ro
Volume=/etc/localtime:/etc/localtime:ro
AddDevice=/dev/ttyAMA0:/dev/ttyAMA0
PodmanArgs=--group-add=keep-groups

[Service]
Restart=always
RestartSec=10

[Install]
WantedBy=default.target
```

## Attivazione

```bash
systemctl --user daemon-reload
systemctl --user enable --now zigbee2mqtt.service
```

L'interfaccia web risponde su `http://raspberrypi.local:8080` e permette di associare i vari dispositivi.

## Riferimenti

- [Zigbee2MQTT - deCONZ](https://www.zigbee2mqtt.io/guide/adapters/deconz.html)
- [RaspBee II - installazione](https://phoscon.de/en/raspbee2/install)
