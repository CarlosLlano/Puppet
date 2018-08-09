# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  
  config.vm.define :puppet_client do |puppet_client|
    puppet_client.vm.box = "ubuntu/xenial64" #https://app.vagrantup.com/ubuntu/boxes/xenial64
    puppet_client.vm.network "public_network"
    puppet_client.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024","--cpus", "1", "--name", "puppet_client" ]
    end  
  end

  config.vm.define :puppet_master do |puppet_master|
    puppet_master.vm.box = "ubuntu/xenial64" #https://app.vagrantup.com/ubuntu/boxes/xenial64
    puppet_master.vm.network "public_network"
    puppet_master.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "4096","--cpus", "4", "--name", "puppet_master" ]
    end  
  end
end

