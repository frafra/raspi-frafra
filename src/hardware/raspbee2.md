# RaspBee II

Istruzioni tratte da [phoscon.de/en/raspbee2/install](https://phoscon.de/en/raspbee2/install).

## Collegamento

Il RaspBee si monta direttamente [sui primi connettori del Raspberry Pi](https://phoscon.de/en/raspbee2/install#connection), ma [solo alcuni sembrano essere utilizzati](https://pinout.xyz/pinout/raspbee).

## Preparazione

Se non lo si è già fatto, abilitare [I²C](../i2c.html).

Bisogna compilare il modulo per il kernel [raspbee2-rtc](https://github.com/dresden-elektronik/raspbee2-rtc/).

```bash
sudo apt-get install build-essential git raspberrypi-kernel-headers
mkdir ~/Code
cd "$_"
git clone https://github.com/dresden-elektronik/raspbee2-rtc.git
cd raspbee2-rtc
make
sudo make install
sudo reboot
```

Verifica:

```bash
sudo hwclock --systohc --verbose
ls -l /dev/ttyAMA0
```

## Zigbee2MQTT

[Zigbee2MQTT](https://www.zigbee2mqtt.io/) è un progetto che permette di interfacciarsi a numerosi dispositivi che dialogano tramite protocollo Zigbee.
Installarlo tramite container rende la procedura più semplice e pulita, senza dover abilitare repository aggiuntivi.

Istruzioni prese da: [www.zigbee2mqtt.io/guide/installation/02_docker.html#rootless-with-podman-3-2](https://www.zigbee2mqtt.io/guide/installation/02_docker.html#rootless-with-podman-3-2)

```bash
sudo apt-get install --no-install-recommends podman uidmap slirp4netns
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
```

### Mosquitto

`mosquitto.conf`:
```
listener 1883
allow_anonymous true
```

```
podman run --rm --name mosquitto --network host -p 1883:1883 -p 9001:9001 -v ~/Code/mosquitto/mosquitto.conf:/mosquitto/config/mosquitto.conf registry.hub.docker.com/library/eclipse-mosquitto
```

### Configurazione sistema

NON SONO SICURO DI NIENTE!!!

> Interfacing Options → Serial
>
>    Would you like a login shell accessible over serial? → No
>
>    Would you like the serial port hardware to be enabled? → Yes


Modificare `/boot/config.txt`, aggiungendo questa sezione:
```
# Disable bluetooth (interference with RaspBee II)
dtoverlay=disable-bt   
```

Riavviare il sistema con `reboot`.

### Avvio di prova

Avviare il container:

```bash
podman run \
   --rm \
   --name=zigbee2mqtt \
   --network host \
   -p 8080:8080 \
   -v ~/Code/zigbee2mqtt/data:/app/data \
   -v /run/udev:/run/udev:ro \
   --device=/dev/ttyAMA0:/dev/ttyAMA0 \
   --group-add keep-groups \
   -e TZ=Europe/Oslo \
   registry.hub.docker.com/koenkk/zigbee2mqtt
```
