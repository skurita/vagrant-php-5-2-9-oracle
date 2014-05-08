# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don"t touch unless you know what you"re doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.
  
  # The hostname the machine should have. Defaults to nil.
  # If nil, Vagrant won't manage the hostname.
  ## If set to a string, the hostname will be set on boot.
  config.vm.hostname = "vagrant-centos58"

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "centos58-chef"

  # The url from where the "config.vm.box" box will be fetched if it
  # doesn"t already exist on the user"s system.
  config.vm.box_url = "https://d2juqhyqspctxc.cloudfront.net/boxes/CentOS-5.8-x86-64-minimal-chef-11-10-4-1.box"
  
  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network :forwarded_port, guest: 80, host: 8888

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  config.vm.network "public_network"
 
  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder ".", "/opt/project", :mount_options => ["dmode=777,fmode=777"]

  # Enable provisioning with chef solo, specifying a cookbooks path, roles
  # path, and data_bags path (all relative to this Vagrantfile), and adding
  # some recipes and/or roles.
  #
  config.vm.provision :chef_solo do |chef|  
    chef.json = {
        :apache => {
            :default_site_enabled => false
        },
        :rpm => [
            "https://d2juqhyqspctxc.cloudfront.net/rpm/php-common-5.2.9-1.x86_64.rpm",
            "https://d2juqhyqspctxc.cloudfront.net/rpm/php-cli-5.2.9-1.x86_64.rpm",
            "https://d2juqhyqspctxc.cloudfront.net/rpm/php-5.2.9-1.x86_64.rpm",
            "https://d2juqhyqspctxc.cloudfront.net/rpm/php-devel-5.2.9-1.x86_64.rpm",
            "https://d2juqhyqspctxc.cloudfront.net/rpm/php-gd-5.2.9-1.x86_64.rpm",
            "https://d2juqhyqspctxc.cloudfront.net/rpm/php-mbstring-5.2.9-1.x86_64.rpm",
            "https://d2juqhyqspctxc.cloudfront.net/rpm/php-pdo-5.2.9-1.x86_64.rpm",
            "https://d2juqhyqspctxc.cloudfront.net/rpm/php-mysql-5.2.9-17.1.x86_64.rpm"
        ],
        :oracle_instantclient => {
            :download_base => "https://d2juqhyqspctxc.cloudfront.net/files",
            :install_dir => "/usr/lib",
            :client_version => "11.1.0.7.0",
            :client_dir_name => "instantclient_11_1"
        }
    }
    
    chef.run_list = [
      "recipe[selinux::disabled]",
      "recipe[iptables::disabled]",
      "recipe[apache2]",
      "recipe[oracle-instantclient]",
      "recipe[oracle-instantclient::sdk]",
      "recipe[oracle-instantclient::sqlplus]",
      "recipe[rpm]",
      "recipe[pdo_oci]"
    ]
    
  end
  
  config.vm.provision :shell, :privileged => false, :inline => <<-EOS
    echo "
        NameVirtualHost *:80
        <VirtualHost *:80>
          ServerName localhost
          DocumentRoot /opt/project/www
          <Directory "/opt/projects/www">
              AllowOverride All
          </Directory>
        </VirtualHost>
    " | sudo tee /etc/httpd/sites-enabled/application.conf
    sudo /etc/init.d/httpd restart
  EOS
end
