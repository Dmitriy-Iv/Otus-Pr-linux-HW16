# -*- mode: ruby -*-
# vim: set ft=ruby :

Vagrant.configure("2") do |config|
    # Base VM OS configuration.
    config.vm.box = "centos/7"
    config.vm.box_version = "2004.01"
    config.vm.provider :virtualbox do |v|
      v.memory = 1024
      v.cpus = 2
    end

    # Define two VMs with static private IP addresses.
    boxes = [
      { :name => "web",
      :ip => "192.168.56.10",
      },
      { :name => "log",
      :ip => "192.168.56.15",
      }
    ]

    # Provision each of the VMs.
      boxes.each do |opts|
        config.vm.define opts[:name] do |config|
          config.vm.hostname = opts[:name]
          config.vm.network "private_network", ip: opts[:ip] 

          config.vm.provision "shell", inline: <<-SHELL
            mkdir -p ~root/.ssh; cp ~vagrant/.ssh/auth* ~root/.ssh
            sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
            systemctl restart sshd
          SHELL

          if opts[:name] == boxes.last[:name]
            config.vm.provision "ansible" do |ansible|
              ansible.playbook = "playbook.yml"
              ansible.inventory_path = "inventories/inventory.yml"
              ansible.host_key_checking = "false"
              ansible.limit = "all"
            end
          end
        end  
      end
end