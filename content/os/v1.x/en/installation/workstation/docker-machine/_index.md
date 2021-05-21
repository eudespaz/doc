---
title: Using Docker Machine
weight: 101
aliases:
  - /os/v1.x/en/installation/running-rancheros/workstation/docker-machine
---

Before we get started, you'll need to make sure that you have docker machine installed. Download it directly from the docker machine [releases](https://github.com/docker/machine/releases). 
You also need to know the [memory requirements]({{<baseurl>}}/os/v1.x/en/#hardware-requirements).

> **Note:** If you create a RancherOS instance using Docker Machine, you will not be able to upgrade your version of RancherOS.

### Downloading RancherOS

Get the latest ISO artifact from the RancherOS [releases](https://github.com/rancher/os).

Machine Driver | Recommended RancherOS version | ISO File
-------------- | ----------------------------- | -------------------------------------------------------------
VirtualBox     | >=v1.0.0 | [rancheros.iso](https://releases.rancher.com/os/latest/rancheros.iso)
VMWare VSphere | >=v1.4.0 | [rancheros-autoformat.iso](https://releases.rancher.com/os/latest/vmware/rancheros-autoformat.iso)
VMWare Fusion  | >=v1.4.0 | [rancheros-autoformat.iso](https://releases.rancher.com/os/latest/vmware/rancheros-autoformat.iso)
Hyper-V        | >=v1.5.0 | [rancheros.iso](https://releases.rancher.com/os/latest/hyperv/rancheros.iso)
Proxmox VE     | >=v1.5.1 | [rancheros-autoformat.iso](https://releases.rancher.com/os/latest/proxmoxve/rancheros-autoformat.iso)

### Using Docker Machine

You can use Docker Machine to launch VMs for various providers. Currently VirtualBox and VMWare(VMWare VSphere, VMWare Fusion) and AWS are supported.

#### Using Docker Machine with VirtualBox

Before moving forward, you'll need to have VirtualBox installed. Download it directly from [VirtualBox](https://www.virtualbox.org/wiki/Downloads). Once you have VirtualBox and Docker Machine installed, it's just one command to get RancherOS running.

Here is an example about using the RancherOS latest link:

```
$ docker-machine create -d virtualbox \
        --virtualbox-boot2docker-url https://releases.rancher.com/os/latest/rancheros.iso \
        --virtualbox-memory <MEMORY-SIZE> \
        <MACHINE-NAME>
```

> **Note:** Instead of downloading the ISO, you can directly use the URL for the `rancheros.iso`.

That's it! You should now have a RancherOS host running on VirtualBox. You can verify that you have a VirtualBox VM running on your host.

> **Note:** After the machine is created, Docker Machine may display some errors regarding creation, but if the VirtualBox VM is running, you should be able to [log in](#logging-into-rancheros).

```
$ VBoxManage list runningvms | grep <MACHINE-NAME>
```

This command will print out the newly created machine. If not, something went wrong with the provisioning step.

#### Using Docker Machine with VMWare VSphere

_Available as of v1.4_

Before moving forward, you’ll need to have VMWare VSphere installed. Once you have VMWare VSphere and Docker Machine installed, it’s just one command to get RancherOS running.

Here is an example about using the RancherOS latest link:

```
$ docker-machine create -d vmwarevsphere \
        --vmwarevsphere-username <USERNAME> \
        --vmwarevsphere-password <PASSWORD> \
        --vmwarevsphere-memory-size <MEMORY-SIZE> \
        --vmwarevsphere-boot2docker-url https://releases.rancher.com/os/latest/vmware/rancheros-autoformat.iso \
        --vmwarevsphere-vcenter <IP-ADDRESS> \
        --vmwarevsphere-vcenter-port <PORT> \
        --vmwarevsphere-disk-size <DISK-SIZE> \
        <MACHINE-NAME>
```

That’s it! You should now have a RancherOS host running on VMWare VSphere. You can verify that you have a VMWare(ESXi) VM running on your host.

#### Using Docker Machine with VMWare Fusion

_Available as of v1.4_

Before moving forward, you’ll need to have VMWare Fusion installed. Once you have VMWare Fusion and Docker Machine installed, it’s just one command to get RancherOS running.

Here is an example about using the RancherOS latest link:

```
$ docker-machine create -d vmwarefusion \
        --vmwarefusion-no-share \
        --vmwarefusion-memory-size <MEMORY> \
        --vmwarefusion-boot2docker-url https://releases.rancher.com/os/latest/vmware/rancheros-autoformat.iso \
        <MACHINE_NAME>
```

That’s it! You should now have a RancherOS host running on VMWare Fusion. You can verify that you have a VMWare Fusion VM running on your host.

#### Using Docker Machine with Hyper-V

_Available as of v1.5_

You should refer to the documentation of [Hyper-V driver](https://docs.docker.com/machine/drivers/hyper-v/), here is an example of using the latest RancherOS URL. We recommend using a specific version so you know which version of RancherOS that you are installing. 

```
$ docker-machine.exe create -d hyperv \
        --hyperv-memory 2048 \
        --hyperv-boot2docker-url https://releases.rancher.com/os/latest/hyperv/rancheros.iso
        --hyperv-virtual-switch <SWITCH_NAME> \
        <MACHINE_NAME>
```
#### Using Docker Machine with Proxmox VE

_Available as of v1.5.1_

There is currently no official Proxmox VE driver, but there is a [choice](https://github.com/lnxbil/docker-machine-driver-proxmox-ve) that you can refer to.

### Logging into RancherOS

Logging into RancherOS follows the standard Docker Machine commands. To login into your newly provisioned RancherOS VM.

```
$ docker-machine ssh <MACHINE-NAME>
```

You'll be logged into RancherOS and can start exploring the OS, This will log you into the RancherOS VM. You'll then be able to explore the OS by [adding system services]({{< baseurl >}}/os/v1.x/en/system-services/), [customizing the configuration]({{< baseurl >}}/os/v1.x/en/configuration/), and launching containers.

If you want to exit out of RancherOS, you can exit by pressing `Ctrl+D`.

### Docker Machine Benefits

With Docker Machine, you can point the docker client on your host to the docker daemon running inside of the VM. This allows you to run your docker commands as if you had installed docker on your host.

To point your docker client to the docker daemon inside the VM, use the following command:

```
$ eval $(docker-machine env <MACHINE-NAME>)
```

After setting this up, you can run any docker command in your host, and it will execute the command in your RancherOS VM.

```
$ docker run -p 80:80 -p 443:443 -d nginx
```

In your VM, a nginx container will start on your VM. To access the container, you will need the IP address of the VM.

```
$ docker-machine ip <MACHINE-NAME>
```

Once you obtain the IP address, paste it in a browser and a _Welcome Page_ for nginx will be displayed.
