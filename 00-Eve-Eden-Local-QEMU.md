# Getting Started with LF-Edge's Eve Project

This is the initial step-by-step process of getting started with
EVE and using its components like:

1. [EVE-OS](https://github.com/lf-edge/eve)
2. [EDEN](https://github.com/lf-edge/eden)
3. [ADAM](https://github.com/lf-edge/adam)

## Initial Diversion

As opposed to directly jumping into building EVE-OS from its
[GitHub Repository](https://github.com/lf-edge/eve) it is
recommended to begin with __EDEN__.

The only thing for this documentation that we refer to from EVE's
repository is its `README.md` for intial dependency installation.

## Host System Overview

During the documenting the system used as a host was as follows:


### Hardware

__Lenovo T14s Laptop with AMD Ryzen 7 Pro processor, 32GB RAM__

Architecture: `x84_64`

### Operating System

__Manjaro Linux Build ID: Rolling__

Kernel Information:

```bash
$ uname -a
Linux my-pc 5.10.53-1-MANJARO #1 SMP PREEMPT Mon Jul 26 07:18:28 UTC 2021 x86_64 GNU/Linux
```

## Dependency Installation for EVE-OS

As mentioned previously we require some dependencies installed
on our host machine.

According to the Documentation of [EVE](https://github.com/lf-edge/eve)
we require:

* `make`
* `qemu`
* `go`
* `docker`
* `jq`
* `git`

### Installation on ArchLinux/Manjaro Distributions

```bash
$ sudo pacman -S make jq git qemu go docker
```

Based on the installations the following versions are installed:

```bash
$ make --version
GNU Make 4.3

$ jq --version
jq-1.6

$ git --version
git version 2.32.0

$ go version
go version go1.16.6 linux/amd64

$ docker version
Client:
 Version:           20.10.7
 API version:       1.41
 Go version:        go1.16.4
 Git commit:        f0df35096d
 Built:             Fri Jun  4 08:14:39 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server:
 Engine:
  Version:          20.10.7
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.4
  Git commit:       b0f5bc36fe
  Built:            Fri Jun  4 08:14:24 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.5.2
  GitCommit:        36cc874494a56a253cd181a1a685b44b58a2e34a.m
 runc:
  Version:          1.0.1
  GitCommit:        v1.0.1-0-g4144b638
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

> __NOTE__: if you go through the documentation for installing other 
    dependencies based on Ubuntu/Debian distributions there are packages
    that are not available in __AUR__ via `pacman`

Here are the packages that were not available:

* `binfmt-support`
* `qemu-user-static`
* `qemu-utils`
* `qemu-system-x86`
* `qemu-system-aarch64`

These packages will be necessary to cross-compile EVE-OS for different
Edge Devices like __Raspberry Pi 4__, __Jetson Nano__ etc.

Upon doing a search via `pacman -Ss qemu` the `qemu-arch-extra` is
installed in order to provide `qemu` for different architectures.

```bash
$ sudo pacman -S qemu-extra-arch
```

This should be able to provide emulators for other architectures on Manjaro

```bash
$ qemu-aarch64 --version
6.0.0
```

## Setup

### Setup Docker Cross-Arch

```bash
$ docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
```

### Setting up Eden

1. clone the repository:

        git clone https://github.com/lf-edge/eden.git && cd eden/

2. in the root of `eden` perform:

        make clean

    The output logs:

    ```
        configName:  default
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
        INFO[0000] cannot stop EVE: cannot open pid file /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.pid: open /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.pid: no such file or directory 
        ....
        ... LOGS reduced for brevity HERE
        ....
        INFO[0000] cannot stop adam: StopAdam: error in rm adam container: container not found 
        INFO[0000] cannot stop redis: StopRedis: error in rm redis container: container not found 
        INFO[0000] cannot stop registry: StopRegistry: error in rm registry container: container not found 
        INFO[0000] cannot stop eserver: StopEServer: error in rm eserver container: container not found 
        INFO[0000] cannot stop EVE: cannot open pid file /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.pid: open /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.pid: no such file or directory 
        INFO[0000] CleanEden done 
    ```
3. Create test configuration for `eden` using:

        make build-tests

    The output logs:

    ```
    INFO[0000] Config file generated: /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0000] Will use config from /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0000] Try to add config from /home/shantanoo/Development/github.com/lf-edge/eden/eden-config.yml 
    INFO[0000] Config file already exists /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0000] Will use config from /home/shantanoo/.eden/contexts/default.yml 
    DEBU[0000] Try to add config from /home/shantanoo/Development/github.com/lf-edge/eden/eden-config.yml 
    ```
4. Let's add the default test configuration to `eden`:

        ./eden config add default

    The output logs:

    ```
    INFO[0000] Config file already exists /home/shantanoo/.eden/contexts/default.yml 
    ```

5. Before deploying anything on an Edge Device let's try emulating the system on our host:

        ./eden setup

    The output logs:

    ```
    INFO[0000] QEMU config file generated: /home/shantanoo/.eden/default-qemu.conf 
    INFO[0007] GenerateEveCerts done                        
    INFO[0007] GenerateEVEConfig done
    ...
    ... Download Logs in JSON Remove here for brevity
    ...
    INFO[0268] Extract layer d63c78d2ff59abb21c20c27cc96b4c0dd403a1302a7391112c3f4d7cbb709ac1/layer.tar 
    INFO[0295] download EVE done: lfedge/eve:0.0.0-master-36a9fae5-kvm-amd64 
    INFO[0295] EVE image ready: /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-images/eve/live.img
    ```

    > __NOTE__: This is the point where a default Image for EVE-OS is created in the `dist/default-images/eve` directory

6. Source the environment variables for activation for `eden`:

        source ~/.eden/activate.sh

7. Let's start `eden`:

        ./eden start
    
    The output logs:

    ```
    INFO[0075] started container: 83fe22b02bf4fe4ed40f14c6bf0d2477ea25c9488b2db59cb5eb5e7ec17586bf 
    INFO[0075] registry is running and accesible on port 5000 
    INFO[0076] started container: 48df95156c72bae59ac1f11f0fd2ca78af308aaa189f94f153e06ff5ee368e75 
    INFO[0076] Eserver is running and accesible on port 8888
    INFO[0076] Start EVE: qemu-system-x86_64 -display none -nodefaults -no-user-config -serial chardev:char0 -chardev socket,id=char0,port=7777,host=localhost,server,nodelay,nowait,telnet,logfile=/home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.log -smbios type=1,serial=31415926 -monitor tcp:localhost:7788,server,nowait  -netdev user,id=eth0,net=192.168.1.0/24,dhcpstart=192.168.1.10,ipv6=off,hostfwd=tcp::5911-:5901,hostfwd=tcp::8027-:8027,hostfwd=tcp::8028-:8028,hostfwd=tcp::2222-:22,hostfwd=tcp::5912-:5902 -device virtio-net-pci,netdev=eth0 -netdev user,id=eth1,net=192.168.1.0/24,dhcpstart=192.168.1.11,ipv6=off,hostfwd=tcp::5922-:5912,hostfwd=tcp::5921-:5911,hostfwd=tcp::8037-:8037,hostfwd=tcp::8038-:8038,hostfwd=tcp::2232-:32 -device e1000,netdev=eth1 -machine q35,accel=kvm,dump-guest-core=off,kernel-irqchip=split -cpu host,invtsc=on,kvmclock=off -device intel-iommu,intremap=on,caching-mode=on,aw-bits=48 -drive file=/home/shantanoo/Development/github.com/lf-edge/eden/dist/default-images/eve/live.img,format=qcow2 -readconfig /home/shantanoo/.eden/default-qemu.conf  
    INFO[0076] With pid: /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.pid ; log: /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.log 
    INFO[0081] EVE is starting 
    ```

    If you go through the logs `eden` is using the `qemu-system-x86_64` emulator to setup `eve`

8. Before we _on-board_ an EVE-OS based emulation machine to `eden` let's go ahead an check the status of `eden`:

        ./eden status # before onboarding
    
    the output logs:

    ```
    ✔ Adam status: container with name eden_adam is running
	Adam is expected at https://192.168.0.104:3333
	For local Adam you can run 'docker logs eden_adam' to see logs
    ✔ Registry status: container with name eden_registry is running
        Registry is expected at https://192.168.0.104:5000
        For local registry you can run 'docker logs eden_registry' to see logs
    ✔ Redis status: container with name eden_redis is running
        Redis is expected at 192.168.0.104:6379
        For local Redis you can run 'docker logs eden_redis' to see logs
    ✔ EServer process status: container with name eden_eserver is running
        EServer is expected at http://192.168.0.104:8888 from EVE
        For local EServer you can run 'docker logs eden_eserver' to see logs

    --- context: default ---
    EVE state: not onboarded

    ? EVE status: undefined (no onboarded EVE)
    ✔ EVE on Qemu status: running with pid 23943
        Logs for local EVE at: /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.log
    ✘ EVE Request IP: error: GetDeviceCurrent error: no device found
    ```

    This is great the other components have start but `eve` has not been on-boarded i.e. no device with `eve` has be found by `eden`

9. Let's on board our emulator device with `eve`:

        ./eden eve onboard

    The out put logs:

    ```
    INFO[0000] Adam waiting for EVE registration (0) of (20) 
    INFO[0020] Adam waiting for EVE registration (1) of (20) 
    INFO[0040] Adam waiting for EVE registration (2) of (20) 
    INFO[0060] Adam waiting for EVE registration (3) of (20) 
    INFO[0080] Adam waiting for EVE registration (4) of (20) 
    INFO[0100] Adam waiting for EVE registration (5) of (20) 
    INFO[0120] Device uuid: 88f579a2-ad3e-49f3-a8a1-bf6c6788c79e 
    .. JSON Dump of a device
    INFO[0121] Received unexpected StatusCode(Bad Request): repeat request (0) of (20) 
    INFO[0126] Received unexpected StatusCode(Bad Request): repeat request (1) of (20) 
    INFO[0131] Received unexpected StatusCode(Bad Request): repeat request (2) of (20) 
    INFO[0136] onboarded                                    
    INFO[0136] device UUID: 88f579a2-ad3e-49f3-a8a1-bf6c6788c79e 
    ```
    After certain amount of retries `adam` is able to find a device `eve` (here emulated on the same network as host machine)

10. Let's check `eden` status after on-boarding:

        ./eden status # after onboarding

    The output logs:

    ```
    ✔ Adam status: container with name eden_adam is running
	Adam is expected at https://192.168.0.104:3333
	For local Adam you can run 'docker logs eden_adam' to see logs
    ✔ Registry status: container with name eden_registry is running
        Registry is expected at https://192.168.0.104:5000
        For local registry you can run 'docker logs eden_registry' to see logs
    ✔ Redis status: container with name eden_redis is running
        Redis is expected at 192.168.0.104:6379
        For local Redis you can run 'docker logs eden_redis' to see logs
    ✔ EServer process status: container with name eden_eserver is running
        EServer is expected at http://192.168.0.104:8888 from EVE
        For local EServer you can run 'docker logs eden_eserver' to see logs

    --- context: default ---
    EVE state: registered

    ✔ EVE REMOTE IPs: 192.168.1.10; fe80::dbd0:2797:dfa9:d39a; 192.168.1.11; fe80::9d7f:29e6:dd5a:7f52
        Last info received time: 2021-07-29 21:44:48 +0200 CEST
    ✔ EVE memory: 418 MB/3.5 GB
    ✔ EVE on Qemu status: running with pid 23943
        Logs for local EVE at: /home/shantanoo/Development/github.com/lf-edge/eden/dist/default-eve.log
    ✔ EVE Request IP: 192.168.0.104
    ------
    ```

    This is great our emulated `eve` device is registered with `eden` and thing look like they might be up for some test-drives

11. The simplest example of deploying and Application on the `eve` device is an `nginx` container. The content of the data to be
    rendered by the HTTP server is in `eden/data/helloeve`

            ./eden pod deploy docker://nginx -p 8028:80 --mount=src=./data/helloeve,dst=/usr/share/nginx/html
    
    This command will mount the data file from your host: `./data/helloeve` to the pod of `nginx` container and make it available
    on port `8028`


    The output logs:

    ```
    INFO[0001] will use volume [./data/helloeve] at mount point [/usr/share/nginx/html]
    ...
    .. JSON logs for deployment
    ... 
    INFO[0003] deploy pod sweet_franklin with docker://nginx request sent
    ```

Voilá ! an `nginx` pod is deployed on your emulated `eve` device.

Let's see if everything is up and running:

```bash
curl -s -XGET http://localhost:8028
```

should produce

```html
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

Seems great. If you visit the link on your browser, the page will display the content
for a certain time and will redirect you to the Eden's GitHub repository.