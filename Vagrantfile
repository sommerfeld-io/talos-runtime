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
    vb.cpus = 5
    vb.memory = 4 * 1024 + 512
    vb.customize ["modifyvm", :id, "--groups", "/vagrantboxes"]
  end

  ## VM-specific setup tasks which most likely are not needed when installing directly on the host
  ## Ubuntu. This is done outside of Ansible to keep the playbook more generic and to bring the VM
  ## to a good base state before Ansible runs.
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y curl
    apt-get autoremove -y
    apt-get autoclean
  SHELL

  ## Allow the kernel to filter and process network traffic passing through bridge interfaces, not
  ## just routed traffic. Enable iptables rules to work on bridged traffic (required by Kubernetes
  ## networking).
  ##
  ## Kubernetes (and therefore Talos) relies on iptables to manage network policies and service
  ## networking. Without `br_netfilter`, iptables rules don't apply to traffic crossing bridge
  ## interfaces, which breaks:
  ## - Pod-to-pod communication
  ## - Service networking
  ## - Network policies
  ## - CoreDNS (which runs in a pod)
  config.vm.provision "shell", inline: <<-SHELL
    sudo modprobe br_netfilter
  SHELL

  ## Ansible Provisioning with basic configuration, installing dependencies as well as all components
  ## required to run and interact with Kubernetes in Talos. The Talos cluster and adjacent tools are
  ## configured and started as well.
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "ansible/playbook.yml"
    ansible.extra_vars = {
      "ansible_user" => "vagrant",
      "ps1" => '${debian_chroot:+($debian_chroot)}\[\033[01;33m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\] $ ',
      "architecture" => "amd64",
    }
  end

  config.vm.network "forwarded_port", guest: 8080, host: 8080 # k8s app: echoserver
  config.vm.network "forwarded_port", guest: 8443, host: 8443 # k8s app: kubernetes-dashboard

end
