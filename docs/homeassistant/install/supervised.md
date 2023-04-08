# Home Assistant Supervised

??? warning "Possibly out of date"

    I don't use this installation type anymore, but this documentation is only kept for others / future reference.

??? warning "Strict requirements"

    This installation type has some strict requirements as outlined by [ADR-0014](https://github.com/home-assistant/architecture/blob/master/adr/0014-home-assistant-supervised.md).

## Debian install

1. Download Debian. I chose the [non-free firmware repo](http://cdimage.debian.org/cdimage/unofficial/non-free/cd-including-firmware/current/amd64/iso-cd/) for maxmimum compatibiliy. This version of Debian contains proprietary kernel modules needed for network connectivity.
2. Put Debian on removable media (eg. thumb drive)
3. Install Debian
    * I use a Network Install
    * I've not setup the `root` user
    * I don't setup an X environment
    * I used the complete disk for provisioning

## Configuration

### Root user

We didn't set up a `root` user during install. This is fine, since we can simply use `sudo`, but prevents us from getting into safe mode when 💩 hits the fan. So we set up a password for the `root` user:

```bash
sudo su
passwd
```

### Swappiness

I lower the swappiness for a more responsive system:

```bash
sudo sysctl -w vm.swappiness=5
sudo echo "vm.swappiness=5" >> /etc/sysctl.conf
```

This will ensure that less of swap is used. You can test if the setting applied correctly: 

```bash
sysctl vm.swappiness
cat /etc/sysctl.conf
```

## Additional / required packages

```bash
sudo apt update
sudo apt install -y aptitude apparmor bash curl dbus dnsutils \
                    git htop jq libglib2.0-bin lsb-release ncdu \
                    nmon network-manager socat sshpass \
                    systemd-journal-remote udisks2 vim wget
```

!!! info
    These are my favorite packages, mixed in with the [required packages](https://github.com/home-assistant/supervised-installer).

## Docker installation

```bash
sudo -s
curl -fsSL get.docker.com | sh
```

## Install Home Assistant OS Agent

You'll need to download the [latest release](https://github.com/home-assistant/os-agent/releases/latest) from GitHub.

```bash
sudo -s
sudo dpkg -i os-agent_1.0.0_linux_x86_64.deb
```

!!! note

    Change the version number to the downloaded version

You can test if the installation was successful by running:

```bash
gdbus introspect --system --dest io.hass.os --object-path /io/hass/os
```

This should **not** return an error. If you get an object introspection with interface etc. OS Agent is working as expected.

## Install Home Assistant

Finally we can download and install Home Assistant:

```bash
wget https://github.com/home-assistant/supervised-installer/releases/latest/download/homeassistant-supervised.deb
apt install ./homeassistant-supervised.deb
```
