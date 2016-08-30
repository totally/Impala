# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "phusion/ubuntu-14.04-amd64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
    vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
    vb.memory = "4096"
  end
  #
  config.vm.provider "vmware_fusion" do |p|
    # Customize the amount of memory on the VM:
    p.vmx["numvcpus"] = "6"
    p.vmx["memsize"] = "4096"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.


  config.vm.provision "chef_solo" do |chef|
    chef.cookbooks_path = "impala-setup/cookbooks"
    chef.json = {
      "run_list": [
        "recipe[impala::default]",
      ],
    }
  end
  config.vm.provision "shell", inline: <<-SHELL


# Copy 'dat Impala
# Using a non-shared filesystem avoids weird issues
mkdir /build || echo "That's ok"
rsync -avr /vagrant/Impala /build
# (cd /vagrant && tar cf - Impala) | (cd /build && tar xf -)

export JAVA_HOME=/usr/lib/jvm/java-7-oracle-amd64
export IMPALA_HOME=/build/Impala
export BOOST_LIBRARYDIR=/usr/lib/x86_64-linux-gnu
export LC_ALL="en_US.UTF-8"
source ${IMPALA_HOME}/bin/impala-config.sh

# Build Impala and prepare the local pseudo-cluster (the first time only)
${IMPALA_HOME}/buildall.sh -noclean -skiptests -build_shared_libs -format

# This script starts a full set of local services including HDFS, HBase,
# Hive and ZooKeeper, amongst other things. If you have trouble starting
# this script, check the log files in ${IMPALA_HOME}/cluster_logs/ for clues.
${IMPALA_HOME}/testdata/bin/run-all.sh


# The following command will start an Impala cluster with 3 Impala demons, one
# Statestore and one Catalog demon.
${IMPALA_HOME}/bin/start-impala-cluster.py
  SHELL
end
