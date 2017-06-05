
    lxc launch ubuntu:xenial conjure-up \
        -p juju-default


    lxc exec conjure-up -- sudo -i -u ubuntu script /dev/null

    sudo apt install squashfuse
    # https://bugs.launchpad.net/snapd/+bug/1668659
    # https://github.com/snapcore/snapd/pull/3136
    sudo mount -o bind,shared /snap /snap

    sudo snap install conjure-up --classic --edge
