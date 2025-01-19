# Train

Notes for learning and be up to date in linux for me.

# Network 
* ip link
* ip addr
* ip --json --brief address show lo

* ip link add name br0 type bridge
* ip link set dev br0 up
* ip link set dev eth0 master br0
* ip link set dev eth1 master br0

* ip link add veth0 type veth peer name veth1
* ip netns add ns1
* ip link set veth0 netns ns1
* ip -n ns1 addr add 10.0.1.0/24 dev veth0
* ip -n ns1 link set veth0 up
* ip -n ns1 link set lo up 
* check with ping! and ip link!

# Virt (anschauen)
* kvm
* proxmox
* libvirt
