

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
        volume.block.mount_options=discard,nobarrier,data=writeback

    lxc network attach-profile lxdbr0 default eth0
    lxc profile device set default eth0 mtu 9000

    lxc profile device add default root disk path=/ pool=default

    lxc network attach lxdbr0 squid-deb-proxy eth0
    lxc config device set squid-deb-proxy eth0 mtu 9000
    lxc config device set squid-deb-proxy eth0 ipv4.address 10.0.8.2

    lxc network attach lxdbr0 mirror eth0
    lxc config device set mirror eth0 mtu 9000
    lxc config device set mirror eth0 ipv4.address 10.0.8.3
