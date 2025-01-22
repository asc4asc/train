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
