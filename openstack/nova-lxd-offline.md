clone

    git clone https://github.com/nobuto-m/openstack-on-lxd.git -b lxd

add model

    juju add-model nova-lxd-offline \
        --config apt-http-proxy= \
        --config apt-mirror=http://10.0.8.3/archive.ubuntu.com/ubuntu

deploy

    juju deploy ./openstack-on-lxd/bundle-ocata-novalxd-offline.yaml
