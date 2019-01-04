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
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "bento/ubuntu-18.04"

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
    config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

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
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  #config.vm.provision "shell", inline: <<-SHELL
  #  cd /home/vagrant/onix.kr.ua/
  #  mkdir mysql
  #  cd mysql
  #  mkdir db_dumps
    config.vm.synced_folder "./", "/home/vagrant"
    config.vm.provision "shell", inline: <<-SHELL
      #mkdir -p /var/lib/mysql
      #chmod -R 777 /var/lib/mysql
      #ln -sf /var/lib/mysql/ /home/vagrant/onix.kr.ua/mysql/mysql_data
      cd /home/vagrant/onix.kr.ua/
      mkdir mysql
      cd mysql
      mkdir db_dumps
      mkdir -p /var/lib/mysql
      chown -R mysql:mysql /var/lib/mysql
      ln -sf /var/lib/mysql/ /home/vagrant/onix.kr.ua/mysql/mysql_data
    SHELL

  # View the documentation for the provider you are using for more
  # information on available options.

  #  config.vm.provision "file",
  #    source: "/sql_dump", destination: "$HOME/vagrant/mysql/db_dumps/sql_dump"
#    source: "d:/Alexandrk/vagrant/vag_00/key/id_rsa.pub", destination: "$HOME/vagrant/id_rsa.pub"
#end
  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
    config.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt install -y apt-transport-https ca-certificates curl software-properties-common
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
      add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
      apt-get update
      apt-cache policy docker-ce
      apt install -y docker-ce
      curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
      chmod +x /usr/local/bin/docker-compose
      apt update
      cd /home/vagrant/onix.kr.ua/
      mv *.sql /home/vagrant/onix.kr.ua/mysql/db_dumps/dump.sql
#      cd /home/alexandrk/vagrant_getting_started/onix.kr.ua/
      chmod +x ./docker-php-pre-entrypoint
      mv .env.default .env
      docker network create traefik_webgateway
      docker-compose up -d mysql56
      sleep 10
      #cd onix.kr.ua
      docker-compose exec -T mysql56 /usr/bin/mysql -u root -e "CREATE DATABASE dbname;"
      docker-compose exec -T mysql56 /usr/bin/mysql -u root -e "CREATE USER 'dbuser'@'%' IDENTIFIED BY 'dbpassword';"
      docker-compose exec -T mysql56 /usr/bin/mysql -u root -e "GRANT ALL PRIVILEGES ON dbname .* TO 'dbuser'@'%';"
      docker-compose exec -T mysql56 /usr/bin/mysql -u root -e "FLUSH PRIVILEGES;"
      docker-compose exec -T mysql56 /usr/bin/mysql -u root -e "SHOW DATABASES;"
      echo "mysql -udbuser -pdbpassword dbname < /db_dumps/dump.sql" | docker-compose exec -T mysql56 bash
      docker-compose up -d
      echo "cd /var/www/html/wp-content/themes/onix-ua/; npm install" | docker-compose exec -T onix.kr.ua bash
      #docker-compose exec -T mysql56 /usr/bin/mysql -u root -e "EXIT;"
      #docker-compose exec -T mysql56 /usr/bin/mysql -u root<<EOF
      #CREATE DATABASE db;
      #CREATE USER 'user'@'%' IDENTIFIED BY 'password';
      #GRANT ALL PRIVILEGES ON db .* TO 'user'@'%';
      #FLUSH PRIVILEGES;
      #SHOW DATABASES;
      #EXIT;
      #EOF
      #sleep 20
      #cd /home/vagrant/onix.kr.ua
      #echo "SET PASSWORD FOR root@'localhost' = PASSWORD(‘admin’);" | docker-compose exec -T mysql56 /usr/bin/mysqladmin -u root -p password
  #    apt install -y git
  #   git clone git@bitbucket.org:onix-systems/onix.kr.ua.git
  #    apt-get update
  #   apt-get install -y apache2
    SHELL
end
