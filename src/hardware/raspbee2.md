# RaspBee II

Istruzioni tratte da [phoscon.de/en/raspbee2/install](https://phoscon.de/en/raspbee2/install).

## Collegamento

Il RaspBee si monta direttamente [sui primi connettori del Raspberry Pi](https://phoscon.de/en/raspbee2/install#connection), ma [solo alcuni sembrano essere utilizzati](https://pinout.xyz/pinout/raspbee).

## Preparazione

Il RaspBee II comunica con il Raspberry Pi tramite la porta seriale. I passi da seguire, in ordine:

1. Abilitare la seriale, usando `sudo raspi-config`:

   > Interfacing Options → Serial
   >
   >    Would you like a login shell accessible over serial? → No
   >
   >    Would you like the serial port hardware to be enabled? → Yes

2. Modificare `/boot/firmware/config.txt`, aggiungendo questa sezione alla fine del file:
   ```
   # Disable bluetooth (interference with RaspBee II)
   dtoverlay=disable-bt
   ```

3. Riavviare il sistema con `reboot` e fare la verifica:

   ```bash
   ls -l /dev/ttyAMA0
   ```

### RTC

Il RaspBee II integra un orologio (RTC). Per usarlo è necessario compilare il modulo per il kernel [raspbee2-rtc](https://github.com/dresden-elektronik/raspbee2-rtc/); una volta installato, il suo stato si controlla con:

```bash
timedatectl
```

## Zigbee2MQTT

[Zigbee2MQTT](https://www.zigbee2mqtt.io/) è un progetto che permette di interfacciarsi a numerosi dispositivi che dialogano tramite protocollo Zigbee.
Installarlo tramite container rende la procedura più semplice e pulita, senza dover abilitare repository aggiuntivi.

```bash
sudo apt install --no-install-recommends podman uidmap slirp4netns
mkdir -p ~/Code/zigbee2mqtt
cd "$_"
mkdir data
```

Creare il file di configurazione `data/configuration.yaml`:

```yaml
# Home Assistant integration (MQTT discovery)
homeassistant: false
# allow new devices to join
permit_join: true
# web interface
frontend: true
# MQTT settings
mqtt:
  # MQTT base topic for zigbee2mqtt MQTT messages
  base_topic: zigbee2mqtt
  # MQTT server URL
  server: 'mqtt://localhost'
  # MQTT server authentication, uncomment if required:
  # user: my_user
  # password: my_password
# Serial settings
serial:
  port: /dev/ttyAMA0
  adapter: deconz
  baudrate: 38400
```

Maggiori informazioni su [Zigbee2MQTT - deCONZ](https://www.zigbee2mqtt.io/guide/adapters/deconz.html).

### Mosquitto

```bash
mkdir -p ~/Code/mosquitto
```

`~/Code/mosquitto/mosquitto.conf`:
```
listener 1883
allow_anonymous true
```

```
podman run --rm --name mosquitto --network host -v ~/Code/mosquitto/mosquitto.conf:/mosquitto/config/mosquitto.conf registry.hub.docker.com/library/eclipse-mosquitto
```

### Avvio di prova

Avviare il container:

```bash
podman run \
   --rm \
   --name=zigbee2mqtt \
   --network host \
   -v ~/Code/zigbee2mqtt/data:/app/data \
   -v /run/udev:/run/udev:ro \
   -v /etc/localtime:/etc/localtime:ro \
   --device=/dev/ttyAMA0:/dev/ttyAMA0 \
   --group-add keep-groups \
   registry.hub.docker.com/koenkk/zigbee2mqtt
```
