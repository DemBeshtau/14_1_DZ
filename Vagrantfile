# -*- mode: ruby -*-
# vi: ft=ruby :

MACHINES = {
    :"monitoring" => {
        :box_name => "ubuntu/jammy64",
        :box_version => "0",
        :cpus => 2,
        :memory => 2048,
    }
}

Vagrant.configure("2") do |config|
    MACHINES.each do |boxname, boxconfig|
        config.vm.define boxname do |box|
            config.vm.synced_folder ".", "/vagrant", disabled: false
            box.vm.box = boxconfig[:box_name]
            box.vm.box_version = boxconfig[:box_version]
            box.vm.host_name = boxname.to_s;
            box.vm.network "forwarded_port", guest: 80, host: 8080
	    box.vm.network "forwarded_port", guest: 9100, host: 9100
	    box.vm.network "forwarded_port", guest: 3000, host: 3000
            box.vm.provider "virtualbox" do |v|
	           # v.gui = true
                v.memory = boxconfig[:memory]
                v.cpus = boxconfig[:cpus]
            end
        end    
    end
    
    #config.vm.provision "shell", path: "config.sh"
end
