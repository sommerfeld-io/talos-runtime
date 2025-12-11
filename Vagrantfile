# -*- mode: ruby -*-
# vi: set ft=ruby :

BOX_NAME = "ubuntu/jammy64" # 22.04 LTS
HOSTNAME = "talos-runtime"

Vagrant.configure("2") do |config|
  config.vm.box = BOX_NAME
  config.vm.hostname = HOSTNAME

  config.vm.provider "virtualbox" do |vb|
    vb.name = HOSTNAME
    vb.gui = false
    vb.cpus = 4
    vb.memory = 4096
    vb.customize ["modifyvm", :id, "--groups", "/vagrantboxes"]
  end

  config.vm.provision "shell", inline: <<-SHELL
    export DEBIAN_FRONTEND=noninteractive

    apt-get update
    apt-get install -y curl
    apt-get autoremove -y
    apt-get autoclean
  SHELL

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "ansible/playbook.yml"
    ansible.extra_vars = {
      "ansible_user" => "vagrant",
      "ps1" => '${debian_chroot:+($debian_chroot)}\[\033[01;33m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\] $ ',
      "architecture" => "amd64",
    }
  end

  config.vm.network "forwarded_port", guest: 9990, host: 6990 # portainer

end
