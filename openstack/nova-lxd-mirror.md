clone

    git clone https://github.com/nobuto-m/openstack-on-lxd.git -b lxd
    cd openstack-on-lxd/

set apt-mirror

    juju add-model openstack

    juju model-config \
        apt-http-proxy= \
        apt-mirror=http://10.0.8.3/archive.ubuntu.com/ubuntu

deploy

    time (juju deploy ./bundle-ocata-novalxd-mirror.yaml && juju wait -v)

fix privileged and nested lxd by updating with -proposed (LP: #1693340)

    juju run --application nova-lxd '
        echo "deb http://10.0.8.3/archive.ubuntu.com/ubuntu/ xenial-proposed main" \
            > /etc/apt/sources.list.d/proposed.list
        apt update
        apt install -y lxd
    '

alloy

    juju config nova-cloud-controller \
        scheduler-default-filters="$(juju config nova-cloud-controller scheduler-default-filters),AggregateInstanceExtraSpecsFilter,NumInstancesFilter,AggregateNumInstancesFilter"

    nova aggregate-create exclusive-lxd
    nova aggregate-set-metadata exclusive-lxd exclusive=true max_instances_per_host=1
    nova aggregate-add-host exclusive-lxd "$(juju run --unit nova-lxd/0 hostname)"

    nova aggregate-create shared-lxd
    nova aggregate-set-metadata shared-lxd exclusive=false
    nova aggregate-add-host shared-lxd "$(juju run --unit nova-lxd/1 hostname)"


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

    nova flavor-create exclusive-lxd auto 16384 3 4
    nova flavor-key exclusive-lxd set aggregate_instance_extra_specs:exclusive=true

    nova flavor-create shared-lxd auto 2048 3 1
    nova flavor-key shared-lxd set aggregate_instance_extra_specs:exclusive=false

    nova keypair-add mykey > ~/.ssh/id_rsa_cloud
