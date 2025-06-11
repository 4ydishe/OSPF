MACHINES = {
  :router1 => {
    :box_name => "ubuntu/focal64",
    :vm_name => "router1",
    :net => [
      {ip: '10.0.10.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "r1-r2"},
      {ip: '10.0.12.1', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "r1-r3"},
      {ip: '192.168.10.1', adapter: 4, netmask: "255.255.255.0", virtualbox__intnet: "net1"},
      {ip: '192.168.50.10', adapter: 5},
    ]
  },
  :router2 => {
    :box_name => "ubuntu/focal64",
    :vm_name => "router2",
    :net => [
      {ip: '10.0.10.2', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "r1-r2"},
      {ip: '10.0.11.2', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "r2-r3"},
      {ip: '192.168.20.1', adapter: 4, netmask: "255.255.255.0", virtualbox__intnet: "net2"},
      {ip: '192.168.50.11', adapter: 5},
    ]
  },
  :router3 => {
    :box_name => "ubuntu/focal64",
    :vm_name => "router3",
    :net => [
      {ip: '10.0.11.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "r2-r3"},
      {ip: '10.0.12.2', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "r1-r3"},
      {ip: '192.168.30.1', adapter: 4, netmask: "255.255.255.0", virtualbox__intnet: "net3"},
      {ip: '192.168.50.12', adapter: 5},
    ]
  }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxconfig[:vm_name]

      boxconfig[:net].each do |ipconf|
        box.vm.network "private_network",
                       ip: ipconf[:ip],
                       netmask: ipconf[:netmask],
                       adapter: ipconf[:adapter],
                       virtualbox__intnet: ipconf[:virtualbox__intnet] || false
      end

      # Configure SSH to allow both key-based and password-based authentication
      box.vm.provision "shell", inline: <<-SHELL
        # Ensure SSH daemon configuration allows both authentication methods
        sudo sed -i 's/#*PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config
        sudo sed -i 's/#*PubkeyAuthentication.*/PubkeyAuthentication yes/' /etc/ssh/sshd_config
        sudo sed -i 's|^Include /etc/ssh/sshd_config.d/\*.conf|#Include /etc/ssh/sshd_config.d/*.conf|' /etc/ssh/sshd_config
        # Set password for vagrant user (default: vagrant)
        echo "vagrant:vagrant" | sudo chpasswd

        # Restart SSH service to apply changes
        sudo systemctl restart sshd
      SHELL

      # Run Ansible playbook on router3 for all routers
      if boxconfig[:vm_name] == "router3"
        box.vm.provision "shell", inline: <<-SHELL
        # Set DNS servers to ensure name resolution
          echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf
          echo "nameserver 8.8.4.4" | sudo tee -a /etc/resolv.conf  
        # Install Ansible if not present
          if ! command -v ansible-playbook &> /dev/null; then
            echo "Installing Ansible..."
            sudo apt-get update
            sudo apt-get install -y software-properties-common
            sudo apt-add-repository --yes --update ppa:ansible/ansible
            sudo apt-get install -y ansible
          fi

          # Run Ansible playbook
          ansible-playbook -i /vagrant/ansible/hosts /vagrant/ansible/provision.yml --extra-vars "host_key_checking=false" --limit all
        SHELL
      end
    end
  end
end