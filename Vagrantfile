# -*- mode: ruby -*-
# vi: set ft=ruby :

NODES = [
    { :hostname => "cliente1", :ip => "192.168.0.11" },
    { :hostname => "cliente2", :ip => "192.168.0.12" },
    { :hostname => "servidor", :ip => "192.168.0.2" }
]

Vagrant.configure("2") do |config|

  # Do whatever global config here
  config.vm.box = "ubuntu/focal64"

  NODES.each do |node|

    config.vm.define node[:hostname] do |nodeconfig|

      # Do config that is the same across each node
      nodeconfig.vm.hostname = node[:hostname]
      nodeconfig.vm.network "public_network"
      nodeconfig.vm.network "private_network", ip: node[:ip]
      nodeconfig.vm.provision "shell", inline: <<-SHELL
        echo -e "\n192.168.0.2 servidor\n192.168.0.11 cliente1\n192.168.0.12 cliente2" | sudo tee -a /etc/hosts
      SHELL

      if node[:hostname] == "servidor"
        # Do your provisioning for this machine here
        nodeconfig.vm.provision "shell", inline: <<-SHELL
          echo "Servidor"
          echo "git cloning slice-enablers.git"
          git clone https://github.com/dcomp-leris/slice-enablers.git --quiet
          chown -R vagrant.vagrant slice-enablers

          echo "apt install iperf"
          bash slice-enablers/scripts/apt.sh

          echo "iniciando servidor iperf"
          bash slice-enablers/scripts/servidor-iperf.sh
        SHELL

      else
        # Do provisioning for the other machines here
        nodeconfig.vm.provision "shell", inline: <<-SHELL
          echo "Cliente"
          echo "git cloning slice-enablers.git"
          git clone https://github.com/dcomp-leris/slice-enablers.git --quiet
          chown -R vagrant.vagrant slice-enablers

          echo "apt install iperf"
          bash slice-enablers/scripts/apt.sh
        SHELL

      end

    end

  end
  # Do any global provisioning

end