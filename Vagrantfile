# -*- mode: ruby -*-
# vi: set ft=ruby :

##### Config

vagrant_project ="project"

##### End of Config

vagrant_user = ENV.fetch("OULIB_USER", "vagrant")
vagrant_command = ARGV[0]
vagrantfile_path = File.dirname(__FILE__)

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

# Recent vagrant releases have been buggy, so we're requiring an exact
# version known to work.
Vagrant.require_version( "=1.8.4")

# If we're doing anything that provisions or reprovisions machines, we
# need to start new versions of the config files that need to know
# about our VMs.
if  ['up', 'reload', 'provision'].include? vagrant_command
  # /etc/hosts file for control machine
  File.open(vagrantfile_path+'/hosts', 'w') do |hosts|
    hosts.puts "127.0.0.1	localhost.localdomain localhost"
  end
  # ~/.ssh/config for vagrant user on control machine
  File.open(vagrantfile_path+'/ssh.cfg', 'w') do |hosts|
    hosts.puts "Host *.vagrant.localdomain"
    hosts.puts "  StrictHostKeyChecking no"
  end
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Default configuration for all VMs
  config.vm.box = "geerlingguy/centos7"
  config.vm.box_version = "1.1.3"
  config.ssh.forward_agent = true
  config.vm.network "private_network", type: "dhcp"
  config.vm.provider :virtualbox do |v|
    v.memory = 512
    v.linked_clone = true
  end

  # All VMs should report in so we can configure them with ansible
  config.vm.provision "shell",
                      path: "scripts/gethostinfo.sh",
                      keep_color: "True",
                      run: "always"

  # Use a "real" user for interactive logins
  if  ['ssh', 'scp'].include? vagrant_command
    # Maybe you want to set this to a real account
    config.ssh.username = vagrant_user
  end


  # Load and build project VMs
  # Use binding.eval to make sure that we're in the right scope.
  binding.eval(File.read(File.expand_path(vagrantfile_path+'/'+ vagrant_project+"/vagrant.rb")))

  # Build Ansible control machine and run vagrant playbook
  config.vm.define "ansible" do |ansible|
    ansible.vm.hostname = "ansible.vagrant.localdomain"
    ansible.vm.provider :virtualbox do |v|
      v.memory = 256  # Keeping overhead low
      v.cpus = 1
    end
    ansible.vm.provision "shell",
                         path: "scripts/bootstrap.sh",
                         keep_color: "True",
                         args: vagrant_project
  end
end
