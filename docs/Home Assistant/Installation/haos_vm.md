# Home Assistant OS

I've used [this community post](https://community.home-assistant.io/t/install-home-assistant-as-a-vm-on-unraid/270195?u=alex3305) as a starting off point for installation.

??? info "Enable VM's in unRAID"

    Before installing this you'll need to [enable VM's in unRAID](https://wiki.unraid.net/Manual/VM_Management).

## Install USB Manager (optional, but recommended)

You can install the USB manager to easily passthrough individual USB devices to your Home Assistant VM (or other VMs). 

1. Go to Apps
2. Search for 'USB Manager'
3. Install 'USB Manager'

## Get qcow2 image

1. Download the latest `qcow2.xz` image from the [Home Assistant OS Release](https://github.com/home-assistant/operating-system/releases/) page on GitHub.
2. Extract this `xz` archive into your unRAID server. I choose the share `domains`

## Setup Home Assistant VM

1. Add a VM
2. Choose the number of CPU cores you want to assign to the VM
3. Choose the amount of RAM you want to assign to the VM
4. Primary vDisk location choose Manual and point it to the `qcow2` file
5. Set Primary vDisk Bus to SATA
6. Click Create

## Setup USB passthrough

!!! note "USB manager required"

    For this step USB manager is required.

For each device you want to passthrough:

1. Click on Device Settings 🖥️
2. Select the Home Assistant Virtual Machine
3. Enable "Auto Connect at USB Device Plugin"
4. Enable "Auto Connect to VM at VM Start"
5. Click Done

??? warning "Conbee II incompatibility"

    For the Conbee II to work properly using the USB Manager on unRAID you'll need to enable "Connect as Serial Only". For more information, see also [this unRAID forum post](https://forums.unraid.net/topic/113301-passthrough-of-conbee-ii-zigbee-usb-gateway-to-home-assistant-virtual-machine/), [this Home Assistant community post](https://community.home-assistant.io/t/solved-conbee-2-fails-to-connect-to-zha-ha-in-unraid-vm/431276?u=alex3305) and [this Reddit post](https://www.reddit.com/r/unRAID/comments/y8jbdk/having_issues_trying_to_get_a_conbee_2_usb_stick/).

## Start VM

Finally you can start the VM. USB devices should connect automatically, as configured. You can use VNC to monitor your VM.
