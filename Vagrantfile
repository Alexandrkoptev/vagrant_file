# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

    config.vm.box = "bento/ubuntu-18.04"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

      config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
      config.vm.network "forwarded_port", guest: 443, host: 443, host_ip: "127.0.0.1"

    #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end

    config.vm.synced_folder "./", "/home/vagrant"
    config.vm.provision "shell", inline: <<-SHELL
          cd /home/vagrant/
      mkdir mysql
      cd mysql
      mkdir db_dumps
      mkdir -p /var/lib/mysql
      chown -R mysql:mysql /var/lib/mysql
      ln -sf /var/lib/mysql/ /home/vagrant/mysql/mysql_data
    SHELL

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
      cd /home/vagrant/
      mv dump.sql /home/vagrant/mysql/db_dumps/dump.sql
      chmod +x ./docker-php-pre-entrypoint
      mv .env.default01 .env
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
      docker-compose -f docker-compose.yml -f docker-compose.vagrant.yml -f docker-compose.traefik.yml up -d
      echo "cd /var/www/html/wp-content/themes/onix-ua/; npm install" | docker-compose exec -T onix.kr.ua bash
      echo "curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar" | docker-compose exec -T onix.kr.ua bash
      echo "chmod +x wp-cli.phar; mv wp-cli.phar /usr/local/bin/wp" | docker-compose exec -T onix.kr.ua bash
      echo "wp --info" | docker-compose exec -T onix.kr.ua bash

    SHELL
end
