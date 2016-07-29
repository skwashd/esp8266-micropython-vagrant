# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/wily64"

  # Forward the NodeMCU to the VM. To use this configuration you need to install
  # the VirtualBox Extension Pack. If you don't want to flash your NodeMCU from
  # the VM you can comment this section.
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--usb", "on"]
    vb.customize ["modifyvm", :id, "--usbehci", "on"]
    vb.customize ['usbfilter', 'add', '0', '--target', :id, '--name', 'QinHeng Electronics USB2.0-Serial', '--product', 'USB2.0-Serial', '--vendorid', '0x1a86', '--productid', '0x7523']
  end

  # Provision script to install dependencies used by the esp-open-sdk and
  # micropython tools.  First install dependencies as root. The rest of the
  # toolkit is downloaded and built.
  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    echo "Installing esp-open-sdk and micropython dependencies..."
    export HOME=/home/vagrant
    sudo adduser vagrant dialout
    sudo apt-get update
    sudo apt-get install -y make unrar autoconf automake libtool gcc g++ gperf flex bison texinfo gawk ncurses-dev libexpat-dev python-dev python python-serial sed git unzip bash help2man wget bzip2 python-pip libtool-bin linux-image-extra-virtual
    cd $HOME
    echo "Installing esp-open-sdk and micropython source..."
    git clone --recursive https://github.com/pfalcon/esp-open-sdk.git
    cd esp-open-sdk
    make STANDALONE=y
    echo "PATH=$(pwd)/xtensa-lx106-elf/bin:\$HOME/.local/bin:\$PATH" >> ~/.profile
    cd $HOME
    git clone https://github.com/micropython/micropython.git
    cd micropython
    git submodule update --init
    cd $HOME
    git clone https://github.com/themadinventor/esptool.git
    cd esptool
    python ./setup.py install --user
    echo "Finished provisioning, now run 'vagrant ssh' to enter the virtual machine."
  SHELL

  # Virtualbox VM configuration.
  config.vm.provider "virtualbox" do |v|
    # Bump the memory allocated to the VM up to 1 gigabyte as the compilation of
    # the esp-open-sdk tools requires more memory to complete.
    v.memory = 1024
  end

end
