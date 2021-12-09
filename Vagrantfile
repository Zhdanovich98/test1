# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "ntp.edu.tentixo.com" do |subconfig|
    subconfig.vm.box = "cmihai/rhel-8-base"
    subconfig.vm.box_version = "1.0"
    subconfig.vm.hostname = "ntp.edu.tentixo.com"
    subconfig.vm.network "private_network", ip: "172.16.1.30"
    subconfig.vm.provision "ansible" do |ansible|
      ansible.playbook = "provision/playbook.yml"
      ansible.verbose = true
      ansible.inventory_path = "files/ansible/inventory"
      ansible.config_file = "files/ansible/ansible.cfg"
    end
  end
end
