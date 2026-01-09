Introduction

This Proxmox-based homelab is designed to host lightweight containers (LXC) and virtual machines (VMs), providing a flexible environment for testing, development, and gaming. This guide covers the setup process, configuration, and advanced networking using Tailscale for secure remote access.

Step 1: Adding the Updates Script

The Proxmox VE helper script PVE Post Install is used to simplify the update process.

<p align="center"> <img width="1444" height="823" src="https://github.com/user-attachments/assets/16bd0b03-223d-47aa-b49a-7ead4828708c" alt="PVE Post Install Script"> </p>

As you can see, the Enterprise repositories are disabled in the configuration when running this script.

<p align="center"> <img width="1918" height="711" src="https://github.com/user-attachments/assets/06ce8266-3313-434e-abb6-98fffcb12a2b" alt="Enterprise Repos Disabled"> </p>

After that, click Updates → Refresh and then Upgrade to keep the system up-to-date. This should be done regularly.

<p align="center"> <img width="1605" height="675" src="https://github.com/user-attachments/assets/e0c41836-6a5a-4cd4-93e8-74a5660956fb" alt="Updates in Proxmox"> </p>
Step 2: Proxmox Features and Future Projects

Proxmox offers several features for future expansion. For example, you could attach a NAS server for storage to handle VM backups and ISO files.
Currently, I don’t have the hardware for these "nice-to-haves," but they will likely be added in the future. For now, storage is local on the Proxmox server.

<p align="center"> <img width="1349" height="376" src="https://github.com/user-attachments/assets/9777b6f1-5bac-4745-a33b-c923e031f4e1" alt="Proxmox Local Storage"> </p>

As shown below, the Ubuntu Server VM is allocated approximately 3 GB of RAM. I chose Ubuntu Server because research shows it is the most stable option.

<p align="center"> <img width="1912" height="176" src="https://github.com/user-attachments/assets/c6eda980-280c-4234-93cb-337a69021b93" alt="Ubuntu Server RAM Allocation"> </p>
Step 3: Creating a VM (Game Server for Project Zomboid)
Quick Setup:

Select the image and leave all other settings at default.

<p align="center"> <img width="716" height="534" src="https://github.com/user-attachments/assets/b3d57fb3-19d7-49d4-8b9d-61f8a70eea76" alt="Select Image for VM"> </p>

All settings in the System tab were also left at default.

In the Disks section, we store the VM on local-lvm, which was automatically created during Proxmox setup. This SSD is used as the primary VM storage, and we allocate 40 GB of space.

<p align="center"> <img width="715" height="532" src="https://github.com/user-attachments/assets/6ce191bb-e8c8-4de6-abca-961bac5f281e" alt="Disk Settings for VM"> </p>

In the CPU section, we assign 2 cores. Project Zomboid is single-core intensive, so more cores would be overkill.
The type is set to HOST to ensure the VM uses real CPU cores instead of a generic virtualized version, which is critical for hosting to maintain correct ticks and performance.

<p align="center"> <img width="718" height="534" src="https://github.com/user-attachments/assets/6004f177-c1be-4a06-83de-d1ae01c80775" alt="CPU Settings for VM"> </p>
Step 4: Network Settings

Network settings are left at default, except the firewall is disabled to reduce overhead.
In Ubuntu, UFW (Uncomplicated Firewall) is configured with the following commands:

sudo ufw allow ssh
sudo ufw allow 16261/tcp
sudo ufw allow 16262/udp
sudo ufw enable


The VM IP is set to 192.168.0.135.
Ubuntu is installed with default VM settings.

<p align="center"> <img width="712" height="533" src="https://github.com/user-attachments/assets/89cf1fe8-17cb-4c40-9c04-b37f3c81e168" alt="Network Settings"> </p>
Step 5: VM Setup Complete

"Success is not the key to happiness. Happiness is the key to success. If you love what you do, you will be successful." – Albert Schweitzer

Ubuntu Server is now fully installed and ready for use.

<p align="center"> <img width="574" height="441" src="https://github.com/user-attachments/assets/761d96a3-e5cc-4124-8ad1-3dcd74ef6615" alt="Ubuntu Server Setup Complete"> </p>

LXC Containers: Basics and Setup

Proxmox also allows you to create containers (CTs), which are lightweight virtual environments that share the host system’s kernel. Unlike virtual machines, containers do not emulate hardware—they share resources directly, making them faster and more efficient for many tasks.

Creating a Container

First, select the storage location for the container.

Go to CT Templates, where you can choose pre-built templates such as Ubuntu or Debian for your container.

The creation process is similar to virtual machines, but containers share the host hardware.

Once created, the container is ready for use. With this, the Proxmox fundamentals and setup are complete.

Remote Access to Proxmox

To securely connect to your Proxmox server remotely, we use Tailscale in an unprivileged LXC container.

LXC vs OCI (Docker) Containers

LXC (Linux Containers): Run a full init system natively.

OCI Containers (Docker): Lightweight, isolated runtime with overlays like S6.

In this tutorial, we will:

Configure an unprivileged LXC for Tailscale

Explain the technical details of /dev/net/tun and kernel networking

Understand why TUN devices require special privileges

Explore how VPN traffic flows through the system

Compare userspace vs kernel networking modes

Step 1: Selecting the Template

We use Debian 12 for the container.

<p align="center"> <img width="897" height="597" src="https://github.com/user-attachments/assets/71baf367-e933-4933-9713-86eb9866c0dd" alt="Select Debian 12 Template"> </p>

General Settings:

<p align="center"> <img width="714" height="531" src="https://github.com/user-attachments/assets/34cec631-2d6e-4216-9aba-4cccff6d8dfd" alt="Container General Settings"> </p>

Template: 12.standard (previously installed)

Storage: Local

Disk size: Default (Tailscale 8 does not require much)

<p align="center"> <img width="717" height="533" src="https://github.com/user-attachments/assets/39f8d80f-bbe7-4793-ae79-7b1b84566a7f" alt="Container Storage Settings"> </p>

CPU: 1 core

RAM: 512 MiB

<p align="center"> <img width="714" height="531" src="https://github.com/user-attachments/assets/e328b882-3a6c-49f4-aaf7-4978b0a91f70" alt="Container CPU and RAM"> </p>

Network: Default, except the IP is dynamically assigned via DHCP

<p align="center"> <img width="712" height="531" src="https://github.com/user-attachments/assets/123f63a7-eccb-4535-ab84-10942d3ee08f" alt="Container Network Settings"> </p>
Step 2: Enabling TUN Devices in the Container

On the Proxmox node shell, edit the container configuration:

vi /etc/pve/lxc/100.conf


Press Shift + G to go to the bottom of the file

Press o to insert

From Tailscale LXC guide
, add the following lines:

lxc.cgroup.devices.allow: c 10:200 rwm
lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file


Explanation:

lxc.cgroup.devices.allow: c 10:200 rwm → Gives the container permission to access TUN devices (rwm = read, write, mknod)

lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file → Binds the host’s TUN device into the container

Press ESC and type :wq to save and exit

The LXC container now has the necessary rights to create a TUN device.

Step 3: Starting the Container
root@pve:~# pct start 1000

<p align="center"> <img width="302" height="41" src="https://github.com/user-attachments/assets/bb402b26-92ef-4d67-99ed-6cc66fb65b3a" alt="Start LXC Container"> </p>

The container is now running.

Step 4: Installing Tailscale in the LXC

Open the container console and run:

apt update
apt install curl
curl -fsSL https://tailscale.com/install.sh | sh
root@lxc-tailscale-01:~# tailscale up --ssh


This sets up Tailscale, enabling secure remote access to your Proxmox server via the container.+


Tailscale VPN Setup and DEVNET TUN

After running the previous tailscale up --ssh command, the following URL was generated for authentication:

https://login.tailscale.com/a/1320f917016fd2?refreshed=true

I added my phone to the Tailscale network. Now, my container and phone can communicate securely, and my phone can connect to the network via VPN to access devices remotely.

DEVNET TUN: How it Works

The DEVNET TUN device acts as a bridge between the kernel and user space, creating a virtual network interface for VPN traffic.

The kernel translates hardware and software requests.

When Tailscale creates a VPN device, it opens /dev/net/tun and applies special configuration to route traffic through the encrypted tunnel.

Normal Networking:

Browser requests google.com

DNS resolves the IP

Kernel performs a routing table lookup

Packet is sent through the matching interface (eth0)

With Tailscale Tunnel:

Browser requests a Tailnet resource

Destination IP is in the CGNAT range (100.64.x.x)

Kernel routes the packet via the tun0 interface

Tailscale intercepts the raw packet, encrypts it, and sends it through the tunnel

On the remote side, Tailscale decrypts and injects the packet into the destination network

The browser is unaware of the VPN; Tailnet handles the routing transparently.

For LXC, creating a privileged container is a security risk if kernel networking devices are not needed. Using an unprivileged LXC is safer.

SSH Configuration

Edit the SSH configuration to allow root login:

root@lxc-tailscale-01:~# nano /etc/ssh/sshd_config

<p align="center"> <img width="1457" height="614" src="https://github.com/user-attachments/assets/6b7198af-c55b-41fe-bfae-8a4ae4eb7f9e" alt="SSH Config"> </p>

Remove # and set PermitRootLogin yes

Save with Ctrl + O → Enter, then Ctrl + X to exit

Remove the IP address assignment in the container to make it static, so it does not change unexpectedly:

<p align="center"> <img width="1080" height="390" src="https://github.com/user-attachments/assets/1a7ef0ec-996d-41b1-9a45-890ffbbca136" alt="Remove IP for static assignment"> </p> <p align="center"> <img width="607" height="266" src="https://github.com/user-attachments/assets/7d8e642a-1346-4d51-b369-52134e2850e6" alt="Static IP configured"> </p>

Verify connectivity with ping:

<p align="center"> <img width="591" height="173" src="https://github.com/user-attachments/assets/9732ebed-02b9-44ea-85dc-6daa7cb1c9a1" alt="Ping Test Successful"> </p>
Enabling Port Forwarding

Edit the system configuration:

root@lxc-tailscale-01:~# nano /etc/sysctl.conf


Uncomment the relevant line to enable IP forwarding

<p align="center"> <img width="1437" height="615" src="https://github.com/user-attachments/assets/32c3350b-7cd9-4592-a510-f68a1d53f5c0" alt="Enable IP Forwarding"> </p>

Enable Tailscale with advertised routes and exit node:

root@lxc-tailscale-01:~# tailscale up --advertise-routes=192.168.0.0/24 --advertise-exit-node


If already up, run:

root@lxc-tailscale-01:~# tailscale set --advertise-routes=192.168.0.0/24 --advertise-exit-node


This allows you to access the entire network via Tailscale.

Tailnet Route Settings

In the Tailscale interface, configure the container for subnet routing:

<p align="center"> <img width="506" height="533" src="https://github.com/user-attachments/assets/71c31caf-14da-4c70-a780-ac0278a30cd0" alt="Tailscale Subnet Routes"> </p>

Subnet routes: Connect to devices that cannot run Tailscale by advertising IP ranges

Exit node: Allow routing of internet traffic through this machine

Now, even when I’m away from home, I can securely connect to my Proxmox server via VPN. Tested successfully with my phone.

Conclusion

This exercise was extensive and successful, covering both basic Proxmox setups and advanced networking with Tailscale.
Further steps could include installing additional services to enhance network security.

<p align="center"> <img width="590" height="1278" src="https://github.com/user-attachments/assets/979e40c7-0707-4b08-950e-288995e45fae" alt="Tailscale Remote Access Successful"> </p>
