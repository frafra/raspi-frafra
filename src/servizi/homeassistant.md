# Home Assistant

[Home Assistant](https://www.home-assistant.io/) è il sistema che raccoglie tutti i sensori e i dispositivi e permette di automatizzarli.

## Servizio

`~/.config/containers/systemd/homeassistant.container`:

```ini
[Unit]
Description=Home Assistant
Requires=mosquitto.service
After=mosquitto.service

[Container]
ContainerName=homeassistant
Image=ghcr.io/home-assistant/home-assistant:stable
Network=host
Volume=%h/Code/homeassistant:/config
Volume=/etc/localtime:/etc/localtime:ro
AddCapability=NET_ADMIN
AddCapability=NET_RAW

[Service]
Restart=always
RestartSec=10

[Install]
WantedBy=default.target
```

## Primo avvio

```bash
mkdir -p ~/Code/homeassistant
systemctl --user daemon-reload
systemctl --user enable --now homeassistant.service
```

La prima configurazione richiede qualche minuto; poi l'interfaccia risponde su `http://raspberrypi.local:8123` e chiede di creare l'utente amministratore.

## Integrazioni

Dall'interfaccia di Home Assistant:

1. L'integrazione MQTT si aggiunge con broker `127.0.0.1` e porta `1883`
2. I dispositivi Zigbee dovrebbero comparire da soli al primo segnale
3. I sensori del [BME280](bme280.md) vengono individuati automaticamente
4. I sensori del [PMS5003](pms5003.md) vengono individuati automaticamente

## Aggiornamenti

```bash
podman pull ghcr.io/home-assistant/home-assistant:stable
systemctl --user restart homeassistant.service
```
