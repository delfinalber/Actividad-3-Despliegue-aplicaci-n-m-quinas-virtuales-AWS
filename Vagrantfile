Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.boot_timeout = 600

  # Puerto Apache+WP
  config.vm.network "forwarded_port", guest: 80, host: 8080

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false  # Cambié a false, quita después
    vb.memory = 2048
    vb.cpus = 2
  end

  # PHP + Apache (ya tienes)
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y apache2 php8.1 libapache2-mod-php8.1 php8.1-mysql php8.1-curl php8.1-gd php8.1-mbstring php8.1-xml php8.1-zip unzip wget
    a2enmod php8.1 rewrite
    systemctl restart apache2
  SHELL

  # WORDPRESS + MySQL COMPLETO
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    DEBIAN_FRONTEND=noninteractive apt-get install -y mysql-server
    
    cd /tmp
    wget https://wordpress.org/latest.tar.gz
    tar -xzf latest.tar.gz
    rm -rf /var/www/html/*
    mv wordpress/* /var/www/html/
    chown -R www-data:www-data /var/www/html/
    chmod -R 755 /var/www/html/
    
    # MySQL WP
    mysql -e "CREATE DATABASE wordpress;"
    mysql -e "CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'password123';"
    mysql -e "GRANT ALL ON wordpress.* TO 'wpuser'@'localhost'; FLUSH PRIVILEGES;"
    
    # Config WP
    cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
    sed -i 's/database_name_here/wordpress/g' /var/www/html/wp-config.php
    sed -i 's/username_here/wpuser/g' /var/www/html/wp-config.php
    sed -i "s/password_here/password123/g" /var/www/html/wp-config.php
    sed -i "s/localhost/localhost/g" /var/www/html/wp-config.php
    
    systemctl restart apache2 mysql
    systemctl enable mysql apache2
  SHELL
end
