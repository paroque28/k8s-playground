# -*- mode: ruby -*-
# # vi: set ft=ruby :

Vagrant.configure(2) do |config|
   if Vagrant.has_plugin?("vagrant-proxyconf")
    config.proxy.http     = "http://proxy-chain.intel.com:911/"
    config.proxy.https    = "http://proxy-chain.intel.com:912/"
    config.proxy.no_proxy = "localhost,127.0.0.1,172.42.42.1,172.42.42.2,172.42.42.3,172.42.42.4,.intel.com"
   end
  (1..3).each do |i|
    config.vm.define "k8s#{i}" do |s|
      s.ssh.forward_agent = true
      s.vm.box = "ubuntu/xenial64"
      s.vm.hostname = "k8s#{i}"
      s.vm.provision :shell, path: "scripts/bootstrap_ansible.sh"
      if i == 1
        s.vm.provision :shell, inline: "PYTHONUNBUFFERED=1 ansible-playbook /vagrant/ansible/k8s-master.yml -c local"
      else
        s.vm.provision :shell, inline: "PYTHONUNBUFFERED=1 ansible-playbook /vagrant/ansible/k8s-worker.yml -c local"
      end
      s.vm.network "private_network", ip: "172.42.42.#{i}", netmask: "255.255.255.0",
        auto_config: true,
        virtualbox__intnet: "k8s-net"
      s.vm.provider "virtualbox" do |v|
        v.name = "k8s#{i}"
        v.memory = 2048
        v.gui = false
      end
    end
  end

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

end
