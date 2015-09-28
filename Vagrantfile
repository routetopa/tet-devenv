# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "hashicorp/precise64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 5000, host: 5000
  config.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

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
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
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
  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    sudo apt-get update
    sudo apt-get install python-dev postgresql libpq-dev python-pip python-virtualenv git-core solr-jetty openjdk-6-jdk -y
    mkdir -p ~/ckan/lib
    sudo ln -s ~/ckan/lib /usr/lib/ckan
    mkdir -p ~/ckan/etc
    sudo ln -s ~/ckan/etc /etc/ckan
    sudo mkdir -p /usr/lib/ckan/default
    sudo chown `whoami` /usr/lib/ckan/default
    virtualenv --no-site-packages /usr/lib/ckan/default
    . /usr/lib/ckan/default/bin/activate
    pip install -e 'git+https://github.com/ckan/ckan.git@ckan-2.4.0#egg=ckan'
    pip install -r /usr/lib/ckan/default/src/ckan/requirements.txt
    deactivate
    . /usr/lib/ckan/default/bin/activate
    sudo apt-get install python-pastescript -y
    sudo -u postgres psql -l
    sudo mkdir -p /etc/ckan/default
    sudo chown -R `whoami` /etc/ckan/
    cd /usr/lib/ckan/default/src/ckan
    sudo cp /vagrant/development.ini /etc/ckan/default/development.ini
    sudo cp /vagrant/jetty /etc/default/jetty
    sudo mv /etc/solr/conf/schema.xml /etc/solr/conf/schema.xml.bak
    sudo ln -s /usr/lib/ckan/default/src/ckan/ckan/config/solr/schema.xml /etc/solr/conf/schema.xml
    sudo service jetty restart
    sudo apt-get install apache2 -y
    sudo apt-get install software-properties-common python-software-properties -y
    sudo add-apt-repository ppa:ondrej/php5 -y
    sudo apt-get update
    sudo apt-get install php5 libapache2-mod-php5 php5-mcrypt curl php5-curl mc git -y
    sudo service apache2 restart
    sudo a2enmod rewrite
    sudo service apache2 restart
    cd /vagrant/
    wget http://wordpress.org/latest.tar.gz
    tar xzvf latest.tar.gz
    sudo apt-get install php5-gd libssh2-php -y
    sudo rm latest.tar.gz
    cd /vagrant/wordpress
    sudo rsync -avP /vagrant/wordpress/ /var/www/
    sudo cp /vagrant/wp-config.php /var/www/
    cd /var/www
    sudo rm index.html
    sudo chown -R vagrant:www-data *
    mkdir /var/www/wp-content/uploads
    sudo chown -R :www-data /var/www/wp-content/uploads
    # composer
    sudo curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/bin
    # ckan-php-client
    sudo cp /vagrant/composer.json /var/www/
    cd /var/www/
    php /bin/composer.phar install
  SHELL
end
