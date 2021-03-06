# Consul Test on pure Vagrant

A Vagrantfile to test different consul scenarios.

# Startup

    $ vagrant up

This will boot 4 machines, on 4 different internal IPs:

* 10.10.10.10 - consul10
* 10.20.20.30 - consul20
* 10.30.30.30 - consul30
* 10.40.40.40 - agent40

From within the machines, you can boot the consul agents:

    vagrant@consul10 $ consul agent -config-dir /etc/consul.d
    vagrant@consul20 $ consul agent -config-dir /etc/consul.d
    vagrant@consul30 $ consul agent -config-dir /etc/consul.d
    vagrant@agent40 $ rm -f /etc/consul.d/server.json && consul agent -config-dir /etc/consul.d

**NOTE**: If your cluster manages to fall into an invalid state, you may `rm -f /var/lib/consul/*` on all machines
in order to restart your cluster in a consistent state.

# dotfiles and such

This Vagrantfile includes my dotfiles. Either switch back to bash, include your own, or simply use mine :)

# LICENSE

This code is placed in the public domain. You may use it as you see fit, for any
purposes. I may not be held responsible for any damages to your infrastructure.
