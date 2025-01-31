# Train

Notes for learning and be up to date in linux for me.
# proxmox
```
pvecm status
pvecm expected 3 im Notfall pvecm expected 1
pvecm nodes
```
### Noch checken
```
systemctl status corosync
systemctl restart corosync
journalctl -b -u corosync
```
# Man braucht mehrere Netzwerkkabel und denn Sinn zu kapieren
qm migrate 106 tre --online --migration_network 10.1.2.0/24

``` 10.1.2.0/24 könnte z.B. eine schelle Direktverbindung sein. ``` 

# Network 
```
ip link
ip addr
ip --json --brief address show lo
```
```
ip link add name br0 type bridge
ip link set dev br0 up
ip link set dev eth0 master br0
ip link set dev eth1 master br0
```
```
ip link add veth0 type veth peer name veth1
ip netns add ns1
ip link set veth0 netns ns1
ip -n ns1 addr add 10.0.1.0/24 dev veth0
ip -n ns1 link set veth0 up
ip -n ns1 link set lo up 
check with ping! and ip link!
```
Stichworte: DHCP, networkmanager, /etv/network/interface/ (.d/), namespaces

# linux live
* debian-live
* toram
* calamares

# Virt (anschauen)
* kvm
* proxmox 
* libvirt
  * virsh list
  * virsh start <guestname>
* docker
* lxc

# chroot
* chroot 
* debootstrap

Platten als file / Virtuelle Maschine benutzt das.
```
mout .... loop
losetup -a

dd if=/dev/zero of=/loopfile bs=1024 count=51200
losetup -f
losetup /dev/loop1 /loopfile

mkfs -t ext4 -v /dev/loop1
mount -t ext4 /dev/loop1 /mnt
```
# Update
* Grafisch mit mehreren reboots. Idioten sicher? Wenn was nicht geht kommt nicht mal ein sichtbarer Fehler? 
* apt update
* apt upgrade

* Unterschiede zu apt dist-upgrade apt-get aptitude ...... ????

# ansible

### local!
```
* ansible -c=local -i localhost, all -m ping
* ansible-playbook -c=local --inventory=127.0.0.1, play.yml 
```
```
ansible -m gather_facts
ansible all -c local -i localhost, -m apt -a upgrade=dist --become # --ask-become-pass ??? Testen !!
```
#### Playbook: play.yml
```
- name Test
  hosts: all
  gather_facts: false
  task:
    - debug: var=hostvars
```
### mit ssh!
* dafür openssh-server installieren.
```
ssh-keygen
cp .ssh/id_rsa.pub .ssh/authorized_keys
Test: ssh localhost
```
### Jetzt ansible
```
ansible-playbook --inventory=localhost, play.yml

echo localhost >> hosts
ansible -i hosts all -m ping


Beispiel: webserver apache/ local 127.0.. oder remote.
```
----
* https://salsa.debian.org/andi/debian-lan-ansible
* git clone https://salsa.debian.org/andi/debian-lan-ansible # dann etwas testen auf frisch installiertem host.

# Some basic Git commands are:
```
git status
git add
git commit
```

### Example Scenario change a disk in a proxmox zfs pool for boot.

If your original boot drive was /dev/sda and it failed, and you replaced it with /dev/sdb, you would:

Offline the failed drive:
```
zpool offline rpool /dev/sda
```
Replace the drive physically.

Partition the new drive:
```
parted /dev/sdb mklabel gpt
```
Replace the drive in the ZFS pool:
zpool replace rpool /dev/sda /dev/sdb

Rebuild the boot loader:
```
proxmox-boot-tool init /dev/sdb2
proxmox-boot-tool refresh
```
This process ensures that your Proxmox setup remains resilient and can recover from drive failures with minimal downtime.

Copilot

If the old drive is completely gone, you can still set up a new boot drive and restore your Proxmox environment. Here’s how you can do it:

Steps to Replace a Completely Failed Boot Drive

Prepare the New Drive:

Install Proxmox on the new drive using the Proxmox installation ISO. Make sure to select UEFI mode if your system uses it.

Recreate the ZFS Pool:

If your ZFS pool was set up with redundancy (e.g., RAID1 or RAIDZ), the data should still be intact on the remaining drives. You can import the existing ZFS pool:
zpool import

Reconfigure Proxmox:

After installing Proxmox, you’ll need to reconfigure it to recognize your existing ZFS pool and VMs. This involves:
Importing the ZFS pool:
```
zpool import <pool_name>
```
Ensuring the pool is set to auto-mount:
```
zpool set cachefile=/etc/zfs/zpool.cache <pool_name>
```
Restore Configuration:

If you have backups of your Proxmox configuration files (e.g., /etc/pve), restore them to the new installation. This will help in restoring your previous settings and VM configurations.

Reinitialize the Boot Loader:

Use the proxmox-boot-tool to set up the boot loader on the new drive:
```
proxmox-boot-tool init /dev/<new_drive_partition>
proxmox-boot-tool refresh
```
Example Scenario

Assuming your original boot drive was /dev/sda and it failed, and you replaced it with /dev/sdb, you would:

Install Proxmox on /dev/sdb.

Import the existing ZFS pool:

zpool import rpool

Set the pool to auto-mount:

zpool set cachefile=/etc/zfs/zpool.cache rpool

Restore any configuration backups you have.

Reinitialize the boot loader:
```
proxmox-boot-tool init /dev/sdb2
proxmox-boot-tool refresh
```
This process should help you get your Proxmox environment back up and running even if the old drive is no longer available

