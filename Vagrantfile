# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  @ip_address = "192.168.33.11"
  @home = "/home/vagrant"
  @rbenv_bin = "#{@home}/.rbenv/bin"
  @rbenv_shims = "#{@home}/.rbenv/shims"
  @rbenv = "#{@rbenv_bin}/rbenv"
  @gem = "#{@rbenv_shims}/gem"
  @pam = "#{@home}/.pam_environment"
  @bashrc = "#{@home}/.bashrc"

  config.vm.box = 'ubuntu/focal64' 
  config.vm.hostname = 'repro'

  config.vm.network 'private_network', ip: @ip_address

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  config.vm.network "forwarded_port", guest: 9292, host: 9292

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  config.vm.provider "virtualbox" do |vb|
    vb.name = "Repro"
    vb.memory = "4096"
    vb.cpus = "4"
    vb.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
    # vb.customize ["modifyvm", :id, "--nested-hw-virt", "on"]
  end

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y build-essential libxml2-dev libxslt1-dev zlib1g-dev libssl-dev libreadline-dev
    apt-get install -y firefox-geckodriver chromium-chromedriver
  SHELL

  config.vm.provision "shell", privileged: false, inline: <<-SHELL 
    git clone https://github.com/rbenv/rbenv.git .rbenv
    mkdir -p $(#{@rbenv} root)/plugins
    git clone https://github.com/rbenv/ruby-build.git $(#{@rbenv} root)/plugins/ruby-build

    #{@rbenv} install 2.6.5
    #{@rbenv} rehash
    #{@rbenv} global 2.6.5
    #{@gem} install bundler
    #{@gem} install rails -v 5.2.5 
    #{@rbenv} rehash

    echo 'export PATH="#{@rbenv_bin}:#{@rbenv_shims}:$PATH"' > #{@bashrc}
    echo 'CAPYBARA_SERVER_HOST=#{@ip_address}' > #{@pam}
  SHELL

end
