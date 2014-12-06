# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.box_check_update = true

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "10.0.0.15"

  config.ssh.forward_agent = true

  config.vm.define "default", autostart: false

  config.vm.define "consul10" do |consul10|
    consul10.vm.network "private_network", ip: "10.10.10.10"
    consul10.vm.hostname = "consul10"
  end

  config.vm.define "consul20" do |consul20|
    consul20.vm.network "private_network", ip: "10.20.20.20"
    consul20.vm.hostname = "consul20"
  end

  config.vm.define "consul30" do |consul30|
    consul30.vm.network "private_network", ip: "10.30.30.30"
    consul30.vm.hostname = "consul30"
  end

  config.vm.define "agent40" do |agent40|
    agent40.vm.network "private_network", ip: "10.40.40.40"
    agent40.vm.hostname = "agent40"
    agent40.vm.network "forwarded_port", guest: 8500, host: 8500
    agent40.vm.provision "shell", inline: "wget --quiet -O /home/vagrant/0.4.1_web_ui.zip https://dl.bintray.com/mitchellh/consul/0.4.1_web_ui.zip"
    agent40.vm.provision "shell", inline: "unzip -d /home/vagrant/ /home/vagrant/0.4.1_web_ui.zip && chown -R vagrant:vagrant /home/vagrant/0.4.1_web_ui.zip /home/vagrant/dist"
  end

  filename = "0.4.1_linux_amd64.zip"
  config.vm.provision "shell", inline: <<-EOSH.gsub(/^ {4}/, "")
    apt-get update -qq -y
    apt-get upgrade -qq -y
    apt-get install -y zip vim-nox zsh git byobu daemontools
    chsh -s /bin/zsh vagrant
    mkdir -p /var/lib/consul /etc/consul.d
    echo > /etc/consul.d/bind.json '{ "bind_addr": "'$( ifconfig | grep eth1 -A1 | tail -n 1 | cut -d : -f 2 | awk '{print $1}' )'" }'
    echo > /etc/consul.d/server.json '{ "bootstrap_expect": 3, "server": true }'
    cat <<-EOF > /etc/consul.d/base.json
    {
      "data_dir": "/var/lib/consul",
      "retry_join": ["10.10.10.10", "10.20.20.20", "10.30.30.30"]
    }
    EOF
    chown -R vagrant:vagrant /etc/consul.d /var/lib/consul

    if [ ! -f /usr/local/src/#{filename} ]
    then
      wget --quiet --output-document=/usr/local/src/#{filename} https://dl.bintray.com/mitchellh/consul/#{filename}
      # -o = overwrite, no prompt (equivalent to force)
      # -u = update (create or freshen)
      # -d = target directory
      unzip -o -u -d /usr/local/bin/ /usr/local/src/#{filename}
    else
      echo "#{filename} already exists, not downloading or unzipping"
    fi

    if [ ! -d /home/vagrant/dotfiles ]
    then
      git clone git://github.com/francois/dotfiles.git /home/vagrant/dotfiles
      chown -R vagrant:vagrant /home/vagrant/dotfiles
    else
      echo "/home/vagrant/dotfiles already exists: no cloning to do"
    fi

    cat <<-EOF > /etc/hosts
    127.0.0.1 localhost
    10.10.10.10 consul10
    10.20.20.20 consul20
    10.30.30.30 consul30
    10.40.40.40 agent40
    EOF
  EOSH
end
