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
    # Add i386 architecture (required by the Linaro toolchain)
    sudo dpkg --add-architecture i386
    sudo apt-get -y update
    sudo apt-get -y dist-upgrade
    # Basic survival kit
    sudo apt-get -y install htop tmux vim git build-essential ia32-libs bash-completion
    # VLC build requirements
    sudo apt-get -y install libtool pkg-config autoconf checkinstall lua5.1
    # VLC dependencies
    sudo apt-get -y install liba52-0.7.4-dev libdirac-dev libdvdread-dev libkate-dev \
                            libass-dev libbluray-dev libcddb2-dev libdca-dev libfaad-dev \
                            libflac-dev libmad0-dev libmodplug-dev libmpcdec-dev \
                            libmpeg2-4-dev libogg-dev libopencv-dev libpostproc-dev \
                            libshout3-dev libspeex-dev libspeexdsp-dev libssh2-1-dev \
                            liblua5.1-0-dev libopus-dev libschroedinger-dev libsmbclient-dev \
                            libtwolame-dev libx264-dev libxcb-composite0-dev libxcb-randr0-dev \
                            libxcb-xv0-dev libzvbi-dev
fi
rm /tmp/.limit

# Download cross-compiler toolchain
if [ ! -e /opt/rpi ]; then
    sudo mkdir -p /opt/rpi
    cd /opt/rpi
    # use sudo to carry over all permissions
    sudo git clone git://github.com/raspberrypi/tools.git
fi

# Set up environment
if ! grep -q 'arm-bcm2708' /home/vagrant/.bashrc; then
    echo 'export PATH=$PATH:/opt/rpi/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin' >> /home/vagrant/.bashrc
    echo 'export CCPREFIX="/opt/rpi/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/arm-linux-gnueabihf-"' >> /home/vagrant/.bashrc
fi

# Grab and build VLC (NOTE: we're not yet testing for rpi-omxil, since that needs to be patched in for a cross-compile)
if [ ! -e /home/vagrant/vlc ]; then
    source /home/vagrant/.bashrc
    cd /home/vagrant
    git clone git://git.videolan.org/vlc.git
    sudo chown -R vagrant:vagrant vlc
    cd vlc
    export CC=arm-linux-gnueabihf-gcc
    ./bootstrap
    ./configure --target=arm-linux-gnueabihf --prefix=/usr --enable-rpi-omxil --enable-dvbpsi --enable-x264 --disable-ogg --disable-mux_ogg --enable-run-as-root --disable-qt --disable-ncurses
    # make
    # sudo checkinstall --fstrans=no --install=yes --pkgname=vlc --pkgversion "1:2.2.0-git`date +%Y%m%d`-0.0raspbian" --default
fi

END
end
