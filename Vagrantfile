# -*- mode: ruby -*-
# vi: set ft=ruby  :

NODES = 2
IMAGE_NAME = "generic/ubuntu2004"
CONTROLPLANE_MEMORY=4096
WORKER_MEMORY=2048
NETWORK_PREFIX="192.168.165"
VM_GROUP_NAME="cka"

Vagrant.configure("2") do |config|

    #config.ssh.insert_key = false
    config.vm.define "k8s-controlplane" do |controlplane|
        controlplane.vm.box = IMAGE_NAME
        controlplane.vm.network "private_network", ip: "#{NETWORK_PREFIX}.10"
        controlplane.vm.hostname = "k8s-controlplane"
        controlplane.vm.provider :libvirt do |vb|
            vb.title = "k8s-controlplane"
            vb.memory = CONTROLPLANE_MEMORY
            vb.cpus = 2
        end        
        controlplane.vm.provision "ansible" do |ansible|
            ansible.playbook = "kubernetes-setup/k8s-controlplane-playbook.yml"
            ansible.groups = {
                "controlplane" => ["k8s-controlplane"]
            }
            ansible.extra_vars = {
                node_ip: "#{NETWORK_PREFIX}.10",
                k8s_hostname: "k8s-controlplane"
            }
        end
    end

    (1..NODES).each do |i|
        config.vm.define "k8s-worker-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "#{NETWORK_PREFIX}.#{i + 10}"
            node.vm.hostname = "k8s-worker-#{i}"
            node.vm.provider :libvirt do |vb|
                vb.title = "k8s-worker-#{i}"
                vb.memory = WORKER_MEMORY
                vb.cpus = 2
            end                  
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "kubernetes-setup/k8s-worker-playbook.yml"
                ansible.groups = {
                    "workers" => ["k8s-worker-[1:#{NODES}]"]
                }                
                ansible.extra_vars = {
                    node_ip: "#{NETWORK_PREFIX}.#{i + 10}",
                    k8s_hostname: "k8s-worker-#{i}"
                }
            end
        end
    end
end