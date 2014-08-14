# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box      = 'opscode-ubuntu-14.04'
  config.vm.box_url  = 'http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_ubuntu-14.04_chef-provisionerless.box'


  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 5000, host: 5000
  config.vm.network "forwarded_port", guest: 8774, host: 8774

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--memory", "2048"]
  end

  config.vm.provision :shell, :inline => <<-SCRIPT
    
    apt-get update
    apt-get -y install git socat curl wget
    su vagrant -c "git clone https://github.com/openstack-dev/devstack.git"
    cd devstack
    useradd docker
    usermod -a -G docker vagrant
    su vagrant -c "touch local.conf"
    echo "[[local|localrc]]" >> local.conf
    echo DATABASE_PASSWORD=nova >> local.conf
    echo RABBIT_PASSWORD=nova >> local.conf
    echo SERVICE_TOKEN=nova >> local.conf
    echo SERVICE_PASSWORD=nova >> local.conf
    echo ADMIN_PASSWORD=nova >> local.conf
    echo LOGFILE=/vagrant/stack.sh.log >> local.conf
    echo SCREEN_LOGDIR=/vagrant/logs/screen >> local.conf
    echo LOGDAYS=1 >> local.conf
    su vagrant -c "./stack.sh"
    
    su vagrant -c "export OS_AUTH_URL=http://127.0.0.1:5000/"
  SCRIPT

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

end
