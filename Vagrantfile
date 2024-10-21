# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"
  config.vm.define "master" do |master|
    master.vm.network "private_network", ip: "192.168.57.103"
    master.vm.hostname= "tierra.sistema.test"
    master.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y bind9 dnsutils
  SHELL
    master.vm.provision "shell", name: "master-dns", inline: <<-SHELL
      cp -v /vagrant/master/named /etc/default/
      cp -v /vagrant/master/named.conf.options /etc/bind/
      cp -v /vagrant/master/named.conf.local /etc/bind/
      cp -v /vagrant/master/db.192.168.57.dns /var/lib/bind/
      systemctl restart named
    SHELL
  end
  
   config.vm.define "slave" do |slave|
     slave.vm.network "private_network", ip: "192.168.57.102"
     slave.vm.hostname= "venus.sistema.test"
     slave.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y bind9 dnsutils
  SHELL
      slave.vm.provision "shell", name: "master-dns", inline: <<-SHELL
      cp -v /vagrant/slave/named /etc/default/
      cp -v /vagrant/slave/named.conf.options /etc/bind/
      cp -v /vagrant/slave/named.conf.local /etc/bind/
      systemctl restart named
    SHELL
  end
end
