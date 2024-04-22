# -*- mode: ruby -*-
# vi: set ft=ruby :


Vagrant.configure("2") do |config|

  # Configuration for master node
  config.vm.define "master" do |master|
    master.vm.box = "ubuntu/focal64"
    master.vm.hostname = "master"
    master.vm.network "private_network", type: "static", ip: "192.168.33.13"
    master.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true
    master.vm.synced_folder ".", "/home/vagrant/shared"
  end

  # Configuration for slave node
  config.vm.define "slave" do |slave|
    slave.vm.box = "ubuntu/focal64"
    slave.vm.hostname = "slave"
    slave.vm.network "private_network", type: "static", ip: "192.168.33.14"
    slave.vm.network "forwarded_port", guest: 80, host: 8081, auto_correct: true
  end

end

