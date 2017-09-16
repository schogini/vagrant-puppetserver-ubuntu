# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.hostname = 'puppet'
  config.vm.box = "ubuntu/trusty64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
      vb.memory = "1024"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
   config.vm.provision "shell", inline: <<-SHELL

    hostname puppet
    export PUPPET_AGENT_VERSION=5.1.0
    export UBUNTU_CODENAME=xenial
    export DUMB_INIT_VERSION=1.2.0
    export PUPPETSERVER_JAVA_ARGS="-Xms512m -Xmx512m" PATH=/opt/puppetlabs/server/bin:/opt/puppetlabs/puppet/bin:/opt/puppetlabs/bin:$PATH PUPPET_HEALTHCHECK_ENVIRONMENT="production"
    apt-get update
    ntpdate pool.ntp.org
    apt-get install --no-install-recommends -y tree nano wget ca-certificates lsb-release ntp
    wget https://apt.puppetlabs.com/puppetlabs-release-pc1-trusty.deb
    dpkg -i puppetlabs-release-pc1-trusty.deb
    apt-get update
    find /etc/puppetlabs/puppet/ssl -name v-puppetnode2.pem -delete
    apt-get install --no-install-recommends -y puppet-agent
    apt-get install --no-install-recommends -y puppetserver 

    sed -i 's/Xms2g/Xms512m/'  /etc/default/puppetserver
    sed -i 's/Xmx2g/Xmx512m/'  /etc/default/puppetserver

    /opt/puppetlabs/bin/puppet resource service puppetserver ensure=running enable=true

    rm -rf $(puppet master --configprint ssldir)
    #puppet cert clean v-puppetnode2

    #/etc/puppetlabs/code/environments/production/manifests/site.pp
    cat > /etc/puppetlabs/code/environments/production/manifests/site.pp <<EOT
node 'v-puppetnode2' {
 include motd
}
node 'puppet' {
 include motd
}
node 'default' { }
EOT


   #puppet module install puppetlabs-motd
   #puppet apply --noop -e "include motd"
   #puppet apply --noop /etc/puppetlabs/code/environments/production/manifests/site.pp
   #puppet agent -t

   #puppet module install puppetlabs-apache
   #puppet apply --noop -e "include apache"

   SHELL

end
