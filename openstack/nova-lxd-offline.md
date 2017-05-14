clone

    git clone https://github.com/nobuto-m/openstack-on-lxd.git -b lxd
    cd openstack-on-lxd/

set apt-mirror

    juju model-config \
        apt-http-proxy= \
        apt-mirror=http://10.0.8.3/archive.ubuntu.com/ubuntu

deploy

    juju deploy ./bundle-ocata-novalxd-offline.yaml

configure
