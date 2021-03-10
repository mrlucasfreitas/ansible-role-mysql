# By: mrlucasfreitas
# https://github.com/mrlucasfreitas

# Install vagrant-hostsupdater plugin
unless Vagrant.has_plugin? 'vagrant-hostsupdater'
    system('vagrant plugin install vagrant-hostsupdater')
  end
  
  # Install public key in VM
  # The public key must be located in ~/.ssh/id_rsa
  ssh_pub_key = File.read("#{Dir.home}/.ssh/id_rsa.pub")
  
  ssh_setup_script = <<-SCRIPT
    ssh_authorized_keys="/home/vagrant/.ssh/authorized_keys"
    echo '#{ssh_pub_key}' >> "$ssh_authorized_keys"
  SCRIPT
  
  # Ansible variables
  ANSIBLE_CONFIG = File.join(File.dirname(__FILE__), "ansible.cfg")
  ansible_provision_playbook = ENV["ANSIBLE_PROVISION_PLAYBOOK"] || "test.yml"
  
  # Define VM images and hostnames
  hosts = [
    {
      :name     => "ubuntu18",
      :box      => "bento/ubuntu-18.04",
      :box_v    => "202012.21.0",
      :vm_name  => "mysql-ubuntu",
      :hostname => "mysql-ubuntu.local",
      :ip       => "172.16.11.41"
    },
    {
      :name     => "centos6",
      :box      => "bento/centos-6",
      :box_v    => "201807.12.0",
      :vm_name  => "mysql-centos6",
      :hostname => "mysql-centos6.local",
      :ip       => "172.16.11.42"
    },
    {
      :name     => "centos7",
      :box      => "bento/centos-7",
      :box_v    => "202002.04.0",
      :vm_name  => "mysql-centos7",
      :hostname => "mysql-centos7.local",
      :ip       => "172.16.11.43"
    },
    {
      :name     => "centos8",
      :box      => "bento/centos-8",
      :box_v    => "202012.21.0",
      :vm_name  => "mysql-centos8",
      :hostname => "mysql-centos8.local",
      :ip       => "172.16.11.44"
    },
    {
      :name     => "amazonlinux2",
      :box      => "bento/amazonlinux-2",
      :box_v    => "1.2.1",
      :vm_name  => "mysql-amazonlinux2",
      :hostname => "mysql-amazonlinux2.local",
      :ip       => "172.16.11.45"
    }
  ]
  
  # Define VM configuration
  Vagrant.configure("2") do |config|
    hosts.each do |host|
      config.vm.define host[:name] do |m|
        m.vm.box = host[:box]
        m.vm.box_version = host[:box_v]
        m.vm.hostname = host[:hostname]
        m.vm.network :private_network, ip: host[:ip]
        
        m.vm.provision "shell", inline: ssh_setup_script, privileged: false
        
        if host[:name] =~ /^freebsd/ then
          m.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true
          m.ssh.shell = "sh"
          m.vm.base_mac = host[:base_mac]
          m.vm.boot_timeout = 600
        end
  
        m.vm.provider :virtualbox do |vb|
          vb.name = host[:vm_name]
          vb.gui = false
          vb.cpus = 1
          vb.memory = 512
        end
      end
    end
  
  # If ansible exist
    if File.exist?(ANSIBLE_CONFIG)
      config.vm.provision "ansible" do |ansible|
        ansible.playbook = "tests/" + ansible_provision_playbook
        ansible.become = true
  
        ansible.tags = ENV["TAGS"]
        ansible.skip_tags = ENV["SKIP_TAGS"]
        ansible.verbose = ENV["VERBOSE"] || "v"
      end
    end
  end