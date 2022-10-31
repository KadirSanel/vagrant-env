# quick start Vagrantfile

Vagrant.configure("2") do |config|
    config.vm.box_check_update = false
    config.vm.provider "virtualbox" do |vb|
        vb.memory = 4096
        vb.gui = false
        vb.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]
    end
  
    # Shepherd Server
    config.vm.define :devopsenv do |server|
  
        server.vm.box = "ubuntu/focal64"
        server.vm.network :private_network, ip: "192.168.56.10"
        server.ssh.port = 2222
        server.vm.hostname = "dev-env"
        server.vm.synced_folder ".", "/vagrant", disabled: true
    
        # Configure User
        server.vm.provision :shell, inline: <<-SHELL
            useradd -m -s /bin/bash prod
            chpasswd <<< "prod:prod" 
            usermod -aG sudo prod
            cp -pr /home/vagrant/.ssh /home/prod/
            chown -R prod:prod /home/prod
        SHELL

        # Configure Firewall
        server.vm.provision :shell, inline: <<-SHELL
            apt-get update
            apt-get install -y ufw
            ufw allow 443/tcp       # Apache
            ufw allow 3306/tcp      # Mysql
            ufw allow 8080/tcp      # Symfony
            ufw allow 80/tcp        # HTTP
            ufw allow 2222/tcp      # SSH
            ufw allow 2376/tcp      # Docker
        SHELL

        # Install Php 
        server.vm.provision :shell, inline: <<-SHELL
            apt-get update
            apt-get install -y software-properties-common
            add-apt-repository ppa:ondrej/php
            apt-get update
            apt-get install -y php7.4 php7.4-cli php7.4-common php7.4-curl php7.4-dev php7.4-intl php7.4-json php7.4-mysql php7.4-zip
            apt-get install -y php7.4-mysql php7.4-pgsql php7.4-sqlite3 php7.4-sybase php7.4-odbc php7.4-ldap php7.4-imap php7.4-tidy php7.4-gmp php7.4-redis
        SHELL

        # Install Docker
        server.vm.provision :shell, inline: <<-SHELL
            apt-get update
            apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
            curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
            add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
            apt-get update
            apt-get install -y docker-ce docker-ce-cli containerd.io
            usermod -aG docker prod
        SHELL

        # Install Docker Compose
        server.vm.provision :shell, inline: <<-SHELL
            apt-get update
            apt-get install -y python3-pip
            pip3 install docker-compose
        SHELL

        # Install Nodejs
        server.vm.provision :shell, inline: <<-SHELL
            apt-get update
            apt-get install -y curl
            curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
            apt-get install -y nodejs
        SHELL

        # Install Git
        server.vm.provision :shell, inline: <<-SHELL
            apt-get update
            apt-get install -y git
        SHELL

        # Install Composer
        server.vm.provision :shell, inline: <<-SHELL
            apt-get update
            apt-get install -y curl
            curl -sS https://getcomposer.org/installer | php
            mv composer.phar /usr/local/bin/composer
        SHELL

        # Install Mysql
        server.vm.provision :shell, inline: <<-SHELL
            apt-get update
            apt-get install -y mysql-server
            mysql -u root -e "ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';"
            mysql -u root -proot -e "CREATE DATABASE IF NOT EXISTS symfony;"
            mysql -u root -proot -e "CREATE USER 'symfony'@'localhost' IDENTIFIED BY 'symfony';"
            mysql -u root -proot -e "GRANT ALL PRIVILEGES ON symfony.* TO 'symfony'@'localhost';"
            mysql -u root -proot -e "FLUSH PRIVILEGES;"
        SHELL

        # Install Apache
        server.vm.provision :shell, inline: <<-SHELL
            apt-get update
            apt-get install -y apache2
        SHELL
        
        # Install Php Extensions
        server.vm.provision :shell, inline: <<-SHELL
            apt-get update
            apt-get install -y php-mysql php-curl php-gd php-intl php-mbstring php-soap php-xml php-xmlrpc php-zip
        SHELL

        # Install Phpmyadmin
        server.vm.provision :shell, inline: <<-SHELL
            apt-get update
            apt-get install -y phpmyadmin
            ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
        SHELL
    end
end