# -*- mode: ruby -*- 
# vi: set ft=ruby : vsa
Vagrant.configure(2) do |config| 
    config.vm.box = "centos/7" 
    config.vm.box_version = "2004.01" 
    config.vm.provider "virtualbox" do |v| 
    v.memory = 512 
    v.cpus = 1 
    end 
    config.vm.define "backup" do |backup| 
    backup.vm.network "private_network", ip: "192.168.56.160" 
    backup.vm.hostname = "backup" 
    backup.vm.disk :disk, name: "backup", size: "2GB"
    end 
    config.vm.define "client" do |client| 
    client.vm.network "private_network", ip: "192.168.56.150" 
    client.vm.hostname = "client" 
#    client.vm.provision "file", source: "sh", destination: "sh"
    client.vm.provision "ansible" do |ansible|
        ansible.playbook = "ansible/playbook.yml"
        ansible.inventory_path = "ansible/inventory"
        ansible.host_key_checking = "false"
        ansible.limit = "all"
        end
    end 
end 
   