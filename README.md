# Highly Available HAProxy servers with Keepalived

The code will create two VMs with HAProxy and Keepalived and additional two VMs serving simple index.html with nginx. Round robin is configured in HAProxy for accessing the nginx instances. Keepalived sets `ha1` machine as a default Master and quickly switches the traffic to `ha2` instance whenever HAProxy process from `ha1` dies.

# Build

`vagrant up`

# Run

`curl 192.168.33.50`
