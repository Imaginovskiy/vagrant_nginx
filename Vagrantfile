VAGRANT_API_VERSION = "2"

Vagrant.configure(VAGRANT_API_VERSION) do |config|

GLOBAL_BOX = "ubuntu/bionic64"
NUMBER_OF_WORKERS = 2
ANSIBLE_INVENTORY_DIR = "inventory"

  (1..NUMBER_OF_WORKERS).each do |i|
    config.vm.define "application-#{i}" do |application|
      application.vm.provider :virtualbox do |vm|
        vm.name = "application-#{i}"
        vm.customize [
          "modifyvm", :id,
          "--name", "application-#{i}",
          "--memory", 512,
          "--natdnshostresolver1", "on",
          "--cpus", 1,
        ]
      end
      
      application.vm.box = GLOBAL_BOX
      application.vm.hostname = "application-#{i}"
      application.vm.network :private_network, ip: "172.19.120.#{10 + i}"
      application.ssh.forward_agent = true

      application.vm.provision "allow_guest_host_resolution",
        type: "shell",
        inline: 'apt-get install -y avahi-daemon libnss-mdns'
      end
  end

  config.vm.define "loadbalancer" do |loadbalancer|
    loadbalancer.vm.provider :virtualbox do |vm|
      vm.name = "loadbalancer"
      vm.customize [
        "modifyvm", :id,
        "--name", "loadbalancer",
        "--memory", 512,
        "--natdnshostresolver1", "on",
        "--cpus", 1,
      ]
    end

    loadbalancer.vm.box = GLOBAL_BOX
    loadbalancer.vm.hostname = "loadbalancer"
    loadbalancer.vm.network "forwarded_port", guest: 80, host: 8081
    loadbalancer.vm.network :private_network, ip: "172.19.120.10"
    loadbalancer.ssh.forward_agent = true
    #loadbalancer.vm.synched_folder "./data", "/vagrant", type: 'nfs'

    loadbalancer.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = '2.0'
      ansible.verbose = "v"
      ansible.playbook = "provisioning/ansible.yml"
      ansible_inventory_dir = "#{ANSIBLE_INVENTORY_DIR}/vagrant"
      ansible.limit = "all"
      ansible.groups = {
          "application" => ["application-[1:#{NUMBER_OF_WORKERS}]"],
          "loadbalancer" => ["loadbalancer"]
      }
    end
  end
end