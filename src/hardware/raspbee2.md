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

## Modulo del kernel per l'RTC

L'RTC è opzionale e non serve a Zigbee2MQTT: il RaspBee II integra un orologio (RTC), ma per usarlo va compilato un modulo per il kernel seguendo le istruzioni del repository [raspbee2-rtc](https://github.com/dresden-elektronik/raspbee2-rtc/).

Con `timedatectl`, la riga `RTC time` deve mostrare un orario.

La configurazione del servizio è in [Zigbee2MQTT](../servizi/zigbee2mqtt.md).
