#
# Heavily inspired by https://github.com/coreos/coreos-vagrant/blob/master/Vagrantfile
#

$master_instances = 3
$master_update_channel = "stable"
$master_memory = 512
$master_cpus = 1
$master_cloud_config = ".work/user-data.master"

$node_v1_instances = 2
$node_v1_update_channel = "beta"
$node_v1_memory = 512
$node_v1_cpus = 1
$node_v1_cloud_config = ".work/user-data.node-v1"

$node_v2_instances = 2
$node_v2_update_channel = "beta"
$node_v2_memory = 512
$node_v2_cpus = 1
$node_v2_cloud_config = ".work/user-data.node-v2"

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false

  config.vm.box = "coreos-stable"
  config.vm.box_url = "http://stable.release.core-os.net/amd64-usr/current/coreos_production_vagrant.json"

  config.vm.provider :virtualbox do |v|
    v.check_guest_additions = false
    v.functional_vboxsf     = false
  end

  # plugin conflict
  if Vagrant.has_plugin?("vagrant-vbguest") then
    config.vbguest.auto_update = false
  end


  ##
  ## Provision master hosts
  ##

  (1..$master_instances).each do |i|
    config.vm.define vm_name = "master%02d" % [i] do |config|

      config.vm.hostname = vm_name

      config.vm.provider :virtualbox do |vb|
        vb.gui = false
        vb.memory = $master_memory
        vb.cpus = $master_cpus
      end

      ip = "172.17.8.#{i+10}"
      config.vm.network :private_network, ip: ip

      config.vm.provision :file, :source => $master_cloud_config, :destination => "/tmp/vagrantfile-user-data"
      config.vm.provision :shell, :inline => "sed -i 's/%%hostname%%/#{vm_name}/g' /tmp/vagrantfile-user-data"
      config.vm.provision :shell, :inline => "mv /tmp/vagrantfile-user-data /var/lib/coreos-vagrant/", :privileged => true

    end
  end

  ##
  ## Provision node hosts
  ##

  (1..$node_v1_instances).each do |i|
    config.vm.define vm_name = "node%02d" % [i] do |config|

      config.vm.hostname = vm_name

      config.vm.provider :virtualbox do |vb|
        vb.gui = false
        vb.memory = $node_v1_memory
        vb.cpus = $node_v1_cpus
      end

      ip = "172.17.8.#{i+100}"
      config.vm.network :private_network, ip: ip

      config.vm.provision :file, :source => $node_v1_cloud_config, :destination => "/tmp/vagrantfile-user-data"
      config.vm.provision :shell, :inline => "mv /tmp/vagrantfile-user-data /var/lib/coreos-vagrant/", :privileged => true

    end
  end

  ($node_v1_instances+1..$node_v1_instances+$node_v2_instances).each do |i|
    config.vm.define vm_name = "node%02d" % [i] do |config|

      config.vm.hostname = vm_name

      config.vm.provider :virtualbox do |vb|
        vb.gui = false
        vb.memory = $node_v2_memory
        vb.cpus = $node_v2_cpus
      end

      ip = "172.17.8.#{i+100}"
      config.vm.network :private_network, ip: ip

      config.vm.provision :file, :source => $node_v2_cloud_config, :destination => "/tmp/vagrantfile-user-data"
      config.vm.provision :shell, :inline => "mv /tmp/vagrantfile-user-data /var/lib/coreos-vagrant/", :privileged => true

    end
  end

  config.vm.provision :ansible do |ansible|
    ansible.playbook = "provisioning/vagrant-inventory.yml"
    ansible.groups = { "master" => [], "node" => [], "node_v1" => [], "node_v2" => [] }
    (1..$master_instances).each do |i|
      vm_name = "master%02d" % [i]
      ansible.groups = ansible.groups.merge({ "master" => vm_name }){|k, v1, v2| v1.push(v2)}
    end
    (1..$node_v1_instances).each do |i|
      vm_name = "node%02d" % [i]
      ansible.groups = ansible.groups.merge({ "node" => vm_name }){|k, v1, v2| v1.push(v2)}
      ansible.groups = ansible.groups.merge({ "node_v1" => vm_name }){|k, v1, v2| v1.push(v2)}
    end
    ($node_v1_instances+1..$node_v1_instances+$node_v2_instances).each do |i|
      vm_name = "node%02d" % [i]
      ansible.groups = ansible.groups.merge({ "node" => vm_name }){|k, v1, v2| v1.push(v2)}
      ansible.groups = ansible.groups.merge({ "node_v2" => vm_name }){|k, v1, v2| v1.push(v2)}
    end
  end

end
