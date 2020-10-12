Vagrant.configure("2") do |config|
  # When changing the node count, also update the ansible.groups below
  NODES_COUNT = 4

  config.vm.box = "centos/7"

  config.vm.network "private_network", type: "dhcp"

  (1..NODES_COUNT).each do |i|
    config.vm.define "node#{i}" do |node|
      node.vm.hostname = "node#{i}"

      node.vm.provider "virtualbox" do |node|
        node.name = "node#{i}"
        node.cpus = 4
        node.memory = "2048"
      end

      if i == NODES_COUNT
        node.vm.provision "ansible" do |ansible|
          ansible.limit = "all"
          ansible.playbook = "ansible/playbook.yml"
          ansible.groups = {
            "all:vars" => { "role" => "client", "nomad_node_type" => "worker" },
            "servers" => [ "node1" ],  # at the moment, only one master is accepted
            "servers:vars" => { "role" => "server" },
            "gearmans" => [ "node[2:3]" ],
            "gearmans:vars" => { "nomad_node_type" => "gearman" }
          }
        end
      end
    end
  end
end
