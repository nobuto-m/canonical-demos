create kvm machine

    uvt-kvm create livepatch-demo release=xenial

ssh

    uvt-kvm wait --insecure livepatch-demo
    uvt-kvm ssh --insecure livepatch-demo

proxy (optional)

    echo 'Acquire::http::proxy "http://10.0.8.2:8000/";' | sudo tee /etc/apt/apt.conf

install old kernel

    sudo apt install linux-{headers,image}-4.4.0-21-generic

set old kernel as default

    echo 'GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux 4.4.0-21-generic"' | sudo tee /etc/default/grub.d/default.cfg
    sudo update-grub

reboot

    sudo reboot
    uvt-kvm ssh --insecure livepatch-demo

snap install

    sudo snap install canonical-livepatch

enable

    canonical-livepatch enable

status

    canonical-livepatch status --verbose
