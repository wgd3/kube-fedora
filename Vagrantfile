# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

Vagrant.configure("2") do |config|

  machines = [ {:name => 'kube-master', :master => true, :mac => '52:54:00:16:28:b5'},
               {:name => 'kube-minion1', :master => false, :mac => '52:54:00:45:24:14'},
               {:name => 'kube-minion2', :master => false, :mac => '52:54:00:45:24:28'},
             ]

  groups = {}
  groups["master"] = []
  groups["minions"] = []
  
  # define member groups for ansible
  machines.each do |machine|
    if machine[:master]
      groups["master"].push(machine[:name])
    else
      groups["minions"].push(machine[:name])
    end
  end

  # make sure we're using the right stroage pool
  config.vm.provider :libvirt do |libvirt|
    libvirt.storage_pool_name = 'vmstorage'
  end

  # find last machine in array so we know when we've cycled through all members
  last = machines[-1][:name]

  # main definition loop
  machines.each do |machine|
    config.vm.define machine[:name].to_s, primary: machine[:master] do |vmconfig|
      vmconfig.vm.box = "fedora/23-cloud-base"
      vmconfig.vm.hostname = machine[:name].to_s
      vmconfig.vm.provider :libvirt do |domain|
        domain.memory = 3072
        domain.cpus = 2
      end
      vmconfig.vm.network :public_network,
        :dev => "br1",
        :mode => "bridge",
        :type => "bridge",
        :mac => machine[:mac].to_s
      #end 
      #vmconfig.vm.provision :reload
      # if we're on the last VM, trigger ansible run
      if machine[:name] == last
        vmconfig.vm.provision :ansible do |ansible|
          ansible.limit = "all"
          ansible.playbook = "provisioning/playbook.yml"
          ansible.groups = groups
      end

    end

  end
end
end
