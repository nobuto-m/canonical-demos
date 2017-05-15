

    echo 'Acquire::http::proxy "http://10.0.8.2:8000/";' | sudo tee /etc/apt/apt.conf

    sudo apt update
    sudo apt install -t xenial-backports lxd

    lxc network delete lxdbr0

    lxc network create lxdbr0 \
        ipv6.address=none \
        ipv4.address=10.0.8.1/24 \
        ipv4.nat=true \
        ipv4.dhcp.ranges=10.0.8.51-10.0.8.200

    lxc storage create default lvm \
        source=ubuntu-vg \
        lvm.thinpool_name=LXDPool \
        volume.block.mount_options=discard,nobarrier
