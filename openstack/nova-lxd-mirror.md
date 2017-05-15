clone

    git clone https://github.com/nobuto-m/openstack-on-lxd.git -b lxd
    cd openstack-on-lxd/

set apt-mirror

    juju model-config \
        apt-http-proxy= \
        apt-mirror=http://10.0.8.3/archive.ubuntu.com/ubuntu

deploy

    time (juju deploy ./bundle-ocata-novalxd-mirror.yaml && juju wait -v)

configure

    source novarc

    ./neutron-ext-net --network-type flat \
        --gateway 10.0.8.1 --cidr 10.0.8.0/24 \
        --floating-range 10.0.8.201:10.0.8.220 \
        ext_net

    ./neutron-tenant-net \
        --router provider-router \
        -N 10.0.8.1 internal 192.168.0.0/24

    nova flavor-create m1.small auto 2048 3 1

    nova keypair-add mykey > ~/.ssh/id_rsa_cloud
