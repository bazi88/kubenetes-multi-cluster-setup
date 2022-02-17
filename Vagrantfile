NUMBER_WORKER_NODE=2
IP_NETWORK="10.0.0."
IP_START=10

Vagrant.configure("2") do |config|
    config.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update -y
        echo "$IP_NETWORK$((IP_START)) master-node" >> /etc/host
        echo "$IP_NETWORK$((IP_START + 1)) worker-node-01" >> /etc/host
        echo "$IP_NETWORK$((IP_START + 2)) worker-node-02" >> /etc/host
    SHELL
    config.vm.box = "hashicorp/bionic64"
    config.vm.box_check_update = true

    config.vm.define "master" do |master|                          
        master.vm.hostname = "master-node"
        master.vm.network "private_network", ip: IP_NETWORK + "#{IP_START}"
        master.vm.provider "virtualbox" do |vb|
            vb.memory = 4096
            vb.cpus = 2
            vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        end
        master.vm.provision "shell", path: "scripts/common.sh"
        master.vm.provision "shell", path: "scripts/master.sh"
    end

    (1..NUMBER_WORKER_NODE).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.hostname = "worker-node-0#{i}"
            node.vm.network "private_network", ip: IP_NETWORK + "#{IP_START + i}"
            node.vm.provider "virtualbox" do |vb|
                vb.memory = 2048
                vb.cpus = 2
                vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
            end
            node.vm.provision "shell", path: "scripts/common.sh"
            node.vm.provision "shell", path: "scripts/worker.sh"
        end
    end
end