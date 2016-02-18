Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.box_check_update = true
  config.vm.provider "virtualbox" do |vb|
   vb.memory = "1024"
   vb.cpus = 1
  end

  config.vm.provision "shell", inline: <<-SHELL
    set -ux
    apt-get update
    apt-get install -y git curl
    curl -L https://bootstrap.saltstack.com -o install_salt.sh
    SHELL

  config.vm.define "master" do |hostconfig|
    hostconfig.vm.network "private_network", ip: "192.168.42.20"
    hostconfig.vm.hostname = "master"
    hostconfig.vm.provision "shell", inline: <<-SHELL
      set -ux
      sh install_salt.sh -U -M -A 192.168.42.20 -P git v2015.8.6
      mkdir -p /etc/salt/master.d
      ln -s /vagrant/salt/master.conf /etc/salt/master.d/custom.conf
      ln -s /vagrant/salt/roster /etc/salt/roster
      apt-get install python-git
      cd /
      tar -xzf /vagrant/repo.git.tar.gz
      service salt-master restart
      SHELL
  end

  (10..20).each do |i|
    config.vm.define "minion#{i}" do |hostconfig|
      config.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"
        vb.linked_clone = true
      end
      hostconfig.vm.network "private_network", ip: "192.168.42.1#{i}"
      hostconfig.vm.hostname = "minion#{i}"
      hostconfig.vm.provision "shell", inline: <<-SHELL
        set -ux
        sh install_salt.sh -U -M -A 192.168.42.20 -P git v2015.8.6
        SHELL
    end
  end  
end
