# Requisiti

## Sistema operativo

Suggerisco di utilizzare Raspberry Pi Imager per installare Raspberry Pi OS sulla scheda di memoria, in quanto permette di configurare rete wireless, password e accesso tramite SSH tramite il pulsante "Modifica impostazioni".

Ho scelto Raspberry Pi OS Lite (64 bit) basato su Debian 13 Trixie.

## Podman

Podman è necessario per far partire i container:

```bash
sudo apt install --no-install-recommends podman uidmap slirp4netns
```

## Directory

I container si configurano in `~/.config/containers/systemd/`, le unità native (come il timer del BME280) in `~/.config/systemd/user/`:

```bash
mkdir -p ~/.config/containers/systemd ~/.config/systemd/user
```

## Lingering

Per far partire i servizi utente all'avvio bisogna abilitare il lingering:

```bash
sudo loginctl enable-linger "$USER"
```

Per i parametri disponibili nelle unità: [systemd.service](https://www.freedesktop.org/software/systemd/man/latest/systemd.service.html) e [systemd.timer](https://www.freedesktop.org/software/systemd/man/latest/systemd.timer.html).
