# I²C

Per abilitare I²C, necessario per il funzionamento dei vari moduli, bisogna lanciare il programma di configurazione del Raspberry Pi:

```
sudo raspi-config
```

Ecco come navigare attraverso i menù per abilitarlo:

- `3    Interface Options`
- `I5   I2C`
- `<Yes>`
- `<Ok>`
- `<Finish>`

La stessa operazione si può fare evitando il menù: `sudo raspi-config nonint do_i2c 0`. Lo sconsiglio, perché `raspi-config` non pare avere una opzione `--help` per controllare le possibile opzioni.

Infine, per verificare il collegamento:

```bash
sudo apt-get install i2c-tools --no-install-recommends
i2cdetect -y 1
```
