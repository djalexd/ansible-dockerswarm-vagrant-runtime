# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"

  workers = 1
  manager_group = [ "manager" ]
  workers_group = (1..workers).to_a.map { |n| "worker-#{n}" }
  groups = {
    "docker_swarm_manager" => manager_group,
    "docker_swarm_worker" => workers_group,
    "docker_engine" => manager_group + workers_group
  }

  config.vm.define "manager" do |box|
    box.vm.network "private_network", ip: "192.168.50.10"
    box.vm.hostname = "mananger"
  end

  (1..workers).each do |id|
    config.vm.define "worker-#{ id }" do |box|
      box.vm.network "private_network", ip: "192.168.50.#{ 10 + id }"
      box.vm.hostname = "worker-#{ id }"
      
      if id == workers
        box.vm.provision "ansible" do |ansible|
          ansible.limit = "all" # Tell Vagrant not to limit ansible to this host
          ansible.playbook = "play.yml"
          ansible.galaxy_role_file = "requirements.yml"
          ansible.groups = groups
          ansible.extra_vars = {
            docker_swarm_interface: "eth1"
          }
        end
      end
    end
  end
end
