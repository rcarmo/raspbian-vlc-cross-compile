# -*- mode: ruby -*-
# vi: set ft=ruby :

# This Vagrantfile targets the vagrant-lxc provider. It should work on other providers given
# a relatively recent version of Vagrant, but your mileage may vary.

Vagrant.configure("2") do |config|
  config.vm.box = "wheezy64"

  config.vm.provision :shell, :inline => <<END
# Check if we need to perform a weekly upgrade - this also triggers initial provisioning
touch -d '-1 week' /tmp/.limit
if [ /tmp/.limit -nt /var/cache/apt/pkgcache.bin ]; then
    dpkg -add-architecture i386
    apt-get -y update
    apt-get -y dist-upgrade
    apt-get -y install htop tmux vim git build-essential ia32-libs bash-completion
fi
rm /tmp/.limit
if [ ! -e /opt/rpi ]; then
    sudo mkdir -p /opt/rpi
    cd /opt/rpi
    # use sudo to carry over all permissions
    sudo git clone git://github.com/raspberrypi/tools.git
fi
if ! grep -q 'arm-bcm2708' /home/vagrant/.bashrc; then
    echo 'export PATH=$PATH:/opt/rpi/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin' >> /home/vagrant/.bashrc
    echo 'export CCPREFIX="/opt/rpi/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/arm-linux-gnueabihf-"' >> /home/vagrant/.bashrc
fi
END
end
