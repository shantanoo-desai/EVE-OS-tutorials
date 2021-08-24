# Setting Up a Raspberry Pi 4 as an EVE Device and Deploying an `nginx` App

If you haven't gone through the previous posts on trying EVE-OS with QEMU go check
them out first in order to understand what it might feel like working with EVE devices
along with `eden` and `adam`

If you have already gone through it, we will now actually take the QEMU stuff on an 
actual Edge Device i.e., __Raspberry Pi 4 Model B__

## Goal

We want to deploy a simple `nginx` from our Host machine to an EVE device (RPi4) over a
network.

This tutorial expects that the Host machine and the EVE device will be on the same network
for sake of simplicity. 

We will also setup a single RPi4 as an EVE device. In the future we will deploy a cluster of
EVE Devices

## Requirements
Things you might need to follow along

### Hardware

| Hardware               | Quantity    |
|------------------------|-------------|
| __Raspberry Pi 4 Model B 4GB RAM__ | 1 |
| __Micro SD Card 16GB__   | 1 |
| __WLAN Router__ (optional) | 1 |
| __Ethernet RJ-45 Cable__ | 1 |
| __USB-C Power Adapter for Pi w/ O/P 5V/3A__| 1 |

### Software

If your Host machine isn't setup for EVE-OS, go through the `00-Eve-Eden-Local-QEMU.md` tutorial
to install the required software and dependencies.

## Let's Make our Raspberry Pi 4 as an EVE device

We will begin by making a fresh start with `eden`. If you haven't cloned the 
[`eden` repo](https://github.com/lf-edge/eden) on your machine then please go ahead and do that.

1. Clean up initially

    ```bash
        cd ~/eden && make clean
    ```

    You will see similar logs as these:

    ```
    configFile:  /home/shantanoo/.eden/contexts/default.yml
    INFO[0000] Config file generated: /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0000] Will use config from /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0000] Try to add config from /home/shantanoo/Development/github.com/lf-edge/eden/eden-config.yml 
    INFO[0000] Config file already exists /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0000] Will use config from /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0000] Try to add config from /home/shantanoo/Development/github.com/lf-edge/eden/eden-config.yml 
    dist/bin/eden-linux-amd64 stop -v "debug"
    configName:  default
    configFile:  /home/shantanoo/.eden/contexts/default.yml
    DEBU[0000] Will use config from /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0000] Try to add config from /home/shantanoo/Development/github.com/lf-edge/eden/eden-config.yml 
    INFO[0000] adam stopped                                 
    INFO[0000] redis stopped                                
    INFO[0000] registry stopped                             
    INFO[0000] eserver stopped                              
    INFO[0000] cannot stop EVE: cannot open pid file 
    /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.pid: 
        open /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.pid: no such file or directory 

    ....

    INFO[0000] cannot stop adam: StopAdam: error in rm adam container: container not found 
    INFO[0000] cannot stop redis: StopRedis: error in rm redis container: container not found 
    INFO[0000] cannot stop registry: StopRegistry: error in rm registry container: container not found 
    INFO[0000] cannot stop eserver: StopEServer: error in rm eserver container: container not found 
    INFO[0000] cannot stop EVE: 
        cannot open pid file /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.pid: 
        open /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.pid: no such file or directory 
    INFO[0000] CleanEden done                               
    rm -rf dist/bin/eden-linux-amd64 dist/bin/eden  /home/shantanoo/Development/github.com/lf-edge/eden/dist
    ```

    The build system will search for a default configuration YAML file in `~/.eden/` directory and stop any running
    components of the EVE-OS ecosystem. If these components aren't running it will mention that they are not found.

2. Let's build `eden`

    ```bash
    make build
    ```
    This should build an `eden` CLI executable in the root of the directory

3. Add device configuration for Raspberry Pi 4

    ```bash
    ./eden config add default --devmodel RPi4
    ```

    This should add `devmodel` key your default configuration file `~/.eden/contexts/default.yml`. A snippet of the
    `default.yml` file with our `devmodel` configuration is below:

    ```yaml
    # File ~/.eden/contexts/default.yml

    # .. removed other configurations for brevity
    eve:
    #name
    name: 'default'

    #devmodel
    devmodel: 'RPi4'

    #devmodel file overwrite
    devmodelfile: ''

    #EVE arch (amd64/arm64)
    arch: 'arm64'

    #EVE os (linux/darwin)
    os: 'linux'

    #EVE acceleration (set to false if you have problems with qemu)
    accel: true

    #variant of hypervisor of EVE (kvm/xen)
    hv: 'kvm'

    #serial number in SMBIOS
    serial: '*'

    #onboarding certificate of EVE to put into adam
    cert: 'default-certs/onboard.cert.pem'

    #device certificate of EVE to put into adam
    device-cert: 'default-certs/device.cert.pem'

    #EVE pid file
    pid: 'default-eve.pid'

    #EVE log file
    log: 'default-eve.log'

    #EVE firmware
    firmware: [default-images/eve/OVMF_CODE.fd default-images/eve/OVMF_VARS.fd]


    # ... removed other configuration for brevity
    ```

    `eden` has already added some relevant information in the configuration like which Certificates
    are needed to on-board a Raspberry Pi 4 and what architecture/OS is needed. The default certs
    are located in the `dist` directory in the root of `eden` repository on the Host machine

4. If you have clean up using `make clean`, it might be worth executing the following command to enable
   cross-compiling images (Host amd64 -> Target arm64):

   ```bash
    docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
   ```

5. Let's setup `eden` with the default configuration for the Raspberry Pi 4

    ```bash
    ./eden setup --verbosity=debug
    ```
    the verbosity to DEBUG level might be interesting to observe what eden is actually doing. Essentially 
    it will use default configuration file to create an arm64 image

    The Logs look similar to:

    ```
    configName:  default
    configFile:  /home/shantanoo/.eden/contexts/default.yml
    DEBU[0000] Will use config from /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0000] Try to add config from /home/shantanoo/Development/github.com/lf-edge/eden/eden-config.yml 
    DEBU[0000] Will use config from /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0000] Try to add config from /home/shantanoo/Development/github.com/lf-edge/eden/eden-config.yml 
    DEBU[0000] Will use config from /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-config_saved.yml 
    DEBU[0000] Try to add config from /home/shantanoo/Development/github.com/lf-edge/eden/eden-config.yml 
    DEBU[0000] Will use config from /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0000] Try to add config from /home/shantanoo/Development/github.com/lf-edge/eden/eden-config.yml 
    INFO[0000] Config file /home/shantanoo/.eden/contexts/default.yml is the same as 
        /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-config_saved.yml 
    INFO[0000] GenerateEveCerts done                        
    INFO[0000] Certs already exists in certs dir: /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-certs 
    INFO[0000] GenerateEVEConfig done                       
    DEBU[0000] Try ImagePull with (lfedge/eve:0.0.0-master-36a9fae5-kvm-arm64) 
    DEBU[0000] Try to call 'docker run lfedge/eve:0.0.0-master-36a9fae5-kvm-arm64 -f raw live' 
    with volumes map[/in:/home/shantanoo/Development/github.com/lf-edge/eden/dist/default-certs 
    /out:/home/shantanoo/Development/github.com/lf-edge/eden/dist/default-images/eve] 

    --
    INFO[0011] download EVE done: lfedge/eve:0.0.0-master-36a9fae5-kvm-arm64 
    INFO[0011] Write file /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-images/eve/live.img to sd (it is in raw format) 
    DEBU[0011] Will use config from /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0011] Try to add config from /home/shantanoo/Development/github.com/lf-edge/eden/eden-config.yml 
    DEBU[0011] Will use config from /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0011] Try to add config from /home/shantanoo/Development/github.com/lf-edge/eden/eden-config.yml 
    To activate EDEN settings run:
    * for BASH/ZSH -- `source ~/.eden/activate.sh`
    * for TCSH -- `source ~/.eden/activate.csh`
    To deactivate them -- eden_deactivate
    ```
6. Source our Eden Settings

    ```bash
    source ~/.eden/activate.sh
    ```

7. We will find our EVE live image ready to loaded on an SD-Card in the following directory:

    ```bash
    ls -la dist/default-images/eve/
    live.img live.raw
    ```
    We will use the `live.img` file for our Raspberry Pi 4

8. Burn the `live.img` on the SD Card (Beware! not to overwrite on any other filesystem except and your SD Card) 

    ```bash
    lsblk -p # should tell you where is you SD Card e.g. /dev/sdX or /dev/mmcblkY
    ```

    If the SD Card has partitions, it is better to unmount them using

    ```bash
    umount /dev/sda1 && umount /dev/sda2
    ```

    Burn the live image on the complete SD Card using:

    ```bash
    sudo dd if=dist/default-images/eve/live.img of=/dev/sda bs=4M conv=fsync
    ```

    Hurrah! You now have an SD Card with EVE on it ready to be inserted in our Raspberry Pi 4

### On-Boarding our Raspberry Pi 4

In general, every EVE device during initial boot requires a Controller in order to make itself available.

In our case, we will on-board our EVE device with the `eden` controller running on our Host machine

Let's initially, connect our Raspberry Pi 4 with an Ethernet RJ-45 Cable to our Network (WLAN Router)

Before we power up the Pi, let's start our `eden` controller

1. Start `eden`

    ```bash
    ./eden start
    ```
    The output should be:

    ```bash

    ✔ Adam status: container with name eden_adam is running
	Adam is expected at https://192.168.0.102:3333
	For local Adam you can run 'docker logs eden_adam' to see logs
    ✔ Registry status: container with name eden_registry is running
        Registry is expected at https://192.168.0.102:5000
        For local registry you can run 'docker logs eden_registry' to see logs
    ✔ Redis status: container with name eden_redis is running
        Redis is expected at 192.168.0.102:6379
        For local Redis you can run 'docker logs eden_redis' to see logs
    ✔ EServer process status: container with name eden_eserver is running
        EServer is expected at http://192.168.0.102:8888 from EVE
        For local EServer you can run 'docker logs eden_eserver' to see logs

    --- context: default ---
    EVE state: not onboarded

    ? EVE status: undefined (no onboarded EVE)
    ✘ EVE Request IP: error: GetDeviceCurrent error: no device found
    ------
    ```
    This mentions that we currently haven't on-boarded our EVE device. Let's do that

2. Insert the SD Card into the Pi and power it up. If you connect the Pi to a monitor you get to see
   the bootup process logs and what actually happens on the device 
   (Unfortunately I cannot reproduce those logs here.).

   Let's tell `eden` to on-board our EVE device

   ```bash
    ./eden eve onboard
   ```

   The logs look similar to:

   ```
    INFO[0000] Adam waiting for EVE registration (0) of (20) 
    INFO[0020] Device uuid: f187a598-eb68-44b4-94e1-522ea01e2c67 
    {"id":{"uuid":"f187a598-eb68-44b4-94e1-522ea01e2c67","version":"4"},"networks":[{"id":"6822e35f-c1b8-43ca-b344-0bbc0ece8cf1","type":4,"ip":{"dhcp":4,"dhcpRange":{}}},{"id":"6822e35f-c1b8-43ca-b344-0bbc0ece8cf3","type":4,"ip":{"dhcp":4,"dhcpRange":{}},"wireless":{"type":1}}],"reboot":{"counter":1000},"configItems":[{"key":"app.allow.vnc","value":"true"},{"key":"debug.default.loglevel","value":"info"},{"key":"debug.default.remote.loglevel","value":"warning"},{"key":"debug.enable.ssh","value":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCq5NW8ZfEH+5M/fb0xYzNDxC46vbLMP79V1axzQUsYzLbOHsYdEkWjQed/7ks4gmQ62wOJZw/XEk7f8PQ74fMPkHL4BPaSzDWNBEfPbdcTXfJBtlIv3YGzQh5eBhuwIjtGL+z1EFeH2a3WrFBlnw1il4WZtPtKYm0qMnd3x2H3XtUh9FZU4KhyxZq/Zbb9qQkCPfMSuJ/7OKPDfpJ0Iif8954BwryZbGFeMrhONww9rJum0dB7FJpG9J/mVyS0JVVqcGo6NQMqTsO+/mHrn3o7TmhZRR8ezLYTDj54HtxiBtqsrI9/BeVcNQ7lRXdGXndF7d17uUFQSk3/KwXgL+rsKHfW5RX+igcvq8GszLU1MvxnRPynnyQZP7QD33EZL3S44YAfww+aj56vMw8nx2FjGumaJC4MqrMDAGbgfP0lMCuCBIDAOEJnf3Q7hwyqYi8KYi6d93FlfIKsY3MMIOrsBfHtH9Ae7zOCaxTWpppvCskwgm4lH0eeHwJvR2/c7xu08yGwI+2KRME5L6uHSLPryfhwydR/BRtk1DnZ5yfLh3V6iG5nuMJIc8tfinn8ls4tk4UGaT2CmcioZRHiOA3al/X+sXTjiFVAh+nxAi0KX5GlaXW/LBUdhUqlxb/3NlHmizr86IrT1zFoXB1ApzFSlQYYpnHhyKzNc+qa6Al4VQ==\n"},{"key":"newlog.allow.fastupload","value":"true"},{"key":"timer.config.interval","value":"5"},{"key":"timer.metric.interval","value":"10"}],"systemAdapterList":[{"name":"eth0","uplink":true,"networkUUID":"6822e35f-c1b8-43ca-b344-0bbc0ece8cf1"},{"name":"wlan0","networkUUID":"6822e35f-c1b8-43ca-b344-0bbc0ece8cf3"}],"deviceIoList":[{"ptype":1,"phylabel":"eth0","phyaddrs":{"Ifname":"eth0"},"logicallabel":"eth0","assigngrp":"eth0","usage":2,"usagePolicy":{"freeUplink":true}},{"ptype":5,"phylabel":"wlan0","phyaddrs":{"Ifname":"wlan0"},"logicallabel":"wlan0","assigngrp":"wlan0","usage":4,"usagePolicy":{}}],"productName":"RPi4"}
    INFO[0021] Received unexpected StatusCode(Bad Request): repeat request (0) of (20) 
    INFO[0026] Received unexpected StatusCode(Bad Request): repeat request (1) of (20) 
    INFO[0031] onboarded                                    
    INFO[0031] device UUID: f187a598-eb68-44b4-94e1-522ea01e2c67 
   ```

   Great, an EVE device with an UUID `f187a598-eb68-44b4-94e1-522ea01e2c67` has been on-boarded!

3. Check the status to obtain the IP address of our EVE device

    ```bash
    ./eden status
    ✔ Adam status: container with name eden_adam is running
	Adam is expected at https://192.168.0.102:3333
	For local Adam you can run 'docker logs eden_adam' to see logs
    ✔ Registry status: container with name eden_registry is running
        Registry is expected at https://192.168.0.102:5000
        For local registry you can run 'docker logs eden_registry' to see logs
    ✔ Redis status: container with name eden_redis is running
        Redis is expected at 192.168.0.102:6379
        For local Redis you can run 'docker logs eden_redis' to see logs
    ✔ EServer process status: container with name eden_eserver is running
        EServer is expected at http://192.168.0.102:8888 from EVE
        For local EServer you can run 'docker logs eden_eserver' to see logs

    --- context: default ---
    EVE state: registered

    ✔ EVE REMOTE IPs: 192.168.0.103; fe80::de5e:a412:cd16:3beb
        Last info received time: 2021-08-23 17:57:15 +0200 CEST
    ✔ EVE memory: 351 MB/3.5 GB
    ✔ EVE Request IP: 192.168.0.103
    ------
    ```
    There you go! our EVE device has the IP address `192.168.0.103`, if wish to use an IPv6 `fe80::de5e:a412:cd16:3beb`

## Deploying an `nginx` app

We will follow the same path of deploying a simple `nginx` pod on the actual EVE Device using `eden` here

In the `data/` directory you will find a simple `nginx` configuration that will be deployed on the EVE device

1. Deploy an `nginx` pod

    ```bash
    ./eden pod deploy --name=eve_rpi_nginx docker://nginx \
        -p 8027:80 \
        --mount=src=./data/helloeve,dst=/usr/share/nginx/html
    ```
    This command tells `eden` to deploy an `nginx` pod as a docker container with the name `eve_rpi_nginx` and expose
    port `8027` on the device for access. `--mount` provides a volume mount configuration for our `nginx` pod

    We have now deployed our `nginx` pod on our actual EVE device

2. We will have to give `eden` some time till the application pod is completely deployed and running. There are some really
   good safety checks happening under-the-hood that will make this application deployment safe! You can check the status using:

    ```bash
    ./eden pod ps
    NAME		    IMAGE			        UUID					             INTERNAL	    EXTERNAL		    MEMORY	STATE(ADAM)	LAST_STATE(EVE)
    eve_pi_nginx	library/nginx:latest	75de13a4-764f-4b8a-93ac-875eb7390b6e	-:80		192.168.0.103:8027	0 B/0 B	IN_CONFIG	DOWNLOAD_STARTED (39%)
    ```

    Once the deployment is done the output of would look like:

    ```bash
    ./eden pod ps
    NAME         IMAGE			        UUID					              INTERNAL	   EXTERNAL		    MEMORY		  STATE(ADAM)    LAST_STATE(EVE)
    eve_pi_nginx library/nginx:latest	75de13a4-764f-4b8a-93ac-875eb7390b6e	10.11.12.2:80	192.168.0.103:8027	1.2 GB/371 MB	IN_CONFIG	RUNNING
    ```

### Testing the App

Since we know the IP address of our EVE Device

```bash
curl -XGET http://192.168.0.103:8027
<html>
<head>
<title>Go back to eden page</title>
<meta http-equiv="REFRESH" content="0;url=https://github.com/lf-edge/eden">
</head>
<body>
Sample hello eve app
</body>
</html>%
```

### Accessing our EVE Device

for troubleshooting or peeping inside our EVE device

```bash
./eden eve ssh
f187a598-eb68-44b4-94e1-522ea01e2c67:~# uname -a
Linux f187a598-eb68-44b4-94e1-522ea01e2c67 5.10.7-default #1 SMP Fri Jun 25 23:28:03 UTC 2021 aarch64 Linux
```

type `exit` to logout of the SSH Shell


## Clean Up

you can stop / delete the pod

```bash
./eden pod stop eve_rpi_nginx
```

OR

```bash
./eden pod delete --with-volumes eve_rpi_nginx
```