create kvm machine

    [trusty]
    uvt-kvm create livepatch-trusty release=trusty

    [xenial]
    uvt-kvm create livepatch-xenial release=xenial

set cpu passthrough (optional). add `<cpu mode='host-passthrough'/>`, which requires power-off and on

    [trusty]
    virsh edit livepatch-trusty

    [xenial]
    virsh edit livepatch-xenial

ssh

    [trusty]
    uvt-kvm wait --insecure livepatch-trusty
    uvt-kvm ssh --insecure livepatch-trusty

    [xenial]
    uvt-kvm wait --insecure livepatch-xenial
    uvt-kvm ssh --insecure livepatch-xenial

proxy (optional)

    echo 'Acquire::http::proxy "http://10.0.8.2:8000/";' | sudo tee /etc/apt/apt.conf

install old kernel

    [trusty]
    sudo apt update
    sudo apt install linux-{headers,image}-generic-lts-xenial linux-{headers,image}-4.4.0-78-generic

    [xenial]
    sudo apt update
    sudo apt install linux-{headers,image}-4.4.0-77-generic

set old kernel as default

    [trusty]
    echo 'GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux 4.4.0-78-generic"' | sudo tee /etc/default/grub.d/default.cfg
    sudo update-grub

    [xenial]
    echo 'GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux 4.4.0-77-generic"' | sudo tee /etc/default/grub.d/default.cfg
    sudo update-grub

snap install

    sudo apt install snapd

    sudo snap install canonical-livepatch

regenerate uniq ID for trusty and reboot for /snap/bin in $PATH

    [trusty]
    sudo rm /etc/machine-id /var/lib/dbus/machine-id && sudo systemd-machine-id-setup

reboot

    sudo reboot

    [trusty]
    uvt-kvm wait --insecure livepatch-trusty
    uvt-kvm ssh --insecure livepatch-trusty

    ## sometimes restarting the daemon is needed (not sure why atm)
    sudo systemctl restart snap.canonical-livepatch.canonical-livepatchd.service

    [xenial]
    uvt-kvm wait --insecure livepatch-xenial
    uvt-kvm ssh --insecure livepatch-xenial

visit https://auth.livepatch.canonical.com/ and enable

    sudo canonical-livepatch enable <TOKEN>

status

    canonical-livepatch status --verbose

    lsmod | grep livepatch
