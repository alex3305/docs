# Hardware

## Main infrastructure

My main infrastructure is very simple and only consists of 4 devices. 

### ASUS AX86S

This is my [main router](https://www.asus.com/networking-iot-servers/wifi-routers/asus-gaming-routers/rt-ax86u/), which handles my gigabit fiber. It has Wifi 6 with support for 160MHz channel width on 5GHz. I utilize that to have gigabit+ speeds on wifi.

### Gigabyte Brix J4105

I use [this very low power](https://www.gigabyte.com/nl/Mini-PcBarebone/GB-BLCE-4105-rev-10) machine with 16GB to run unRAID, Home Assistant and a dozen or so Docker services. It is also packed with 2 1TB SSDs for backups and data storage.

### QNAP TS-351

This [QNAP NAS](https://www.qnap.com/en-us/product/ts-351) that I bought very cheap on iBood and filled it with 8GB of RAM. This device only has a meager Intel J1800, but that does the job. It is also a bit of a weird device as it houses 3 3.5" drive bays and 2 NVMe slots. In addition I also use USB storage. Currently it has over 30TB of internal storage and about 7TB of external storage. My NAS also runs Docker (Container Station) for a dozen or so Docker containers.

### Raspberry Pi 3B+

I still use my old Home Assistant, a Raspberry Pi 3B+, for my water heater temperature probe.
