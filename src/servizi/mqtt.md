# MQTT (Mosquitto)

[Mosquitto](https://mosquitto.org/) è il broker MQTT scelto, che mette in comunicazione i dispositivi Zigbee e il sensore BME280 con Home Assistant.

Serve Podman, installato come descritto in [Requisiti](../requisiti.md). Il broker va avviato prima di Zigbee2MQTT e Home Assistant, che lo richiamano nelle rispettive unit.

## Configurazione

```bash
mkdir -p ~/Code/mosquitto
```

Il file `~/Code/mosquitto/mosquitto.conf` contiene:

```
listener 1883 127.0.0.1
allow_anonymous true
```

L'ascolto è limitato a `127.0.0.1`: funziona per gli altri servizi, che usano `Network=host`, ma non per dispositivi esterni al Raspberry.

## Servizio

`~/.config/containers/systemd/mosquitto.container`:

```ini
[Unit]
Description=Mosquitto MQTT broker

[Container]
ContainerName=mosquitto
Image=registry.hub.docker.com/library/eclipse-mosquitto:latest
Network=host
Volume=%h/Code/mosquitto/mosquitto.conf:/mosquitto/config/mosquitto.conf

[Service]
Restart=always

[Install]
WantedBy=default.target
```

## Attivazione

```bash
systemctl --user daemon-reload
systemctl --user enable --now mosquitto.service
```

## Aggiornamenti

```bash
podman pull registry.hub.docker.com/library/eclipse-mosquitto:latest
systemctl --user restart mosquitto.service
```
