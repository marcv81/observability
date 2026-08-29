# Telegraf

## Installation

Install the Influxdata repo key.

    sudo curl -fsSL https://repos.influxdata.com/influxdata-archive.key -o /etc/apt/keyrings/influxdata.asc

Create `/etc/apt/sources.list.d/influxdata.sources` with the following contents.

    Types: deb
    URIs: https://repos.influxdata.com/debian
    Suites: stable
    Components: main
    Architectures: amd64
    Signed-By: /etc/apt/keyrings/influxdata.asc

Install Telegraf.

    sudo apt update
    sudo apt install telegraf

## Turbostat

Allow the `telegraf` user to run turbostat in `/etc/sudoers.d/telegraf`.

    telegraf ALL=(root) NOPASSWD: /usr/bin/turbostat

## AMD GPU

Install `rocm-smi`.

    sudo apt install rocm-smi

## SMART

Install smartctl, but disable smartd.

    sudo apt install smartmontools
    sudo systemctl stop smartd
    sudo systemctl disable smartd

Allow the `telegraf` user to run smartctl in `/etc/sudoers.d/telegraf`.

    telegraf ALL=(root) NOPASSWD: /usr/sbin/smartctl

Install nvme-cli.

    sudo apt install nvme-cli

For NVME devices, the unit of `Data_Units_Read` and `Data_Units_Written` is 512000 bytes.

Telegraf runs smartctl via sudo every 10s. This generates noise in the journald logs. Add the following line to `/etc/pam.d/common-session-noninteractive`, just before `session required pam_unix.so`.

    session [success=1 default=ignore] pam_succeed_if.so quiet uid = 0 ruser = telegraf

## Nuvoton NCT6796D-S

Install lm-sensors.

    sudo apt install lm-sensors

The module does not load automatically. Create `/etc/modules-load.d/nct6775.conf` with the following contents.

    nct6775

Reload the modules.

    sudo systemctl restart systemd-modules-load

For my motherboard and case, we have the following sensors.

- Temperature sensors
  - `tsi0`,`smbusmaster_0`: CPU die
  - `systin`, `cputin`: Motherboard
  - `auxtin*`, `pch_*`: Garbage values
- Fans
  - `fan2`: `CPU`
  - `fan4`: `CHA1` (bottom)
  - `fan5`: `CHA2` (back)
  - `fan7`: `CHA3` (top)

## Configuration

Install the configuration.

    sudo cp config/telegraf/telegraf.conf /etc/telegraf/telegraf.conf

Start and enable the service.

    sudo systemctl start telegraf
    sudo systemctl enable telegraf
