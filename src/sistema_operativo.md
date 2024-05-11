# Sistema operativo

Suggerisco di utilizzare Raspberry Pi Imager per installare Raspbian sulla scheda di memoria, in quanto permette di configurare rete wireless, password e accesso tramite SSH premendo sulla ruota dentata in basso a destra.

Ho scelto Raspbian Debian 12 Bookworm. Raspbian basata su Bookworm non è ancora stata rilasciata (14/08/2023), ma si può aggiornare dalla versione 11:

```bash
sudo sed -i.bak 's/bullseye/bookworm/g' /etc/apt/sources.list
sudo apt-get update
sudo apt-get dist-upgrade --no-install-recommends
sudo apt-get autoremove
```

Non modifico anche il file `/etc/apt/sources.list.d/raspi.list` perché contiene `raspberrypi-kernel` e `raspberrypi-kernel-headers`.

Bookworm include una versione più recente di Podman, che permette di esporre facilmente le periferiche ai programmi che girano nei container.

Ho provato a installare Debian Bookworm direttamente sul Raspberry Pi con una daily image, scritta con Raspberry Pi Imager, ma pare che qualcosa non sia andato a buon fine, in quanto non sono riuscito a contattare il dispositivo tramite `raspberrypi.local`.
