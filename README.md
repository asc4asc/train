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
