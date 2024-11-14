# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "debian/bullseye64"
    config.vm.network "private_network", ip: "192.168.33.10"
    config.vm.provision "shell", inline: <<-SHELL

    # Actualizar repositorios e instalar Nginx
        apt-get update
        apt-get install -y nginx git vsftpd openssl

    # Crear carpetas del sitio web
    mkdir -p /var/www/resetdefabrica/html
    cd /var/www/resetdefabrica/html
    git clone https://github.com/cloudacademy/static-website-example /var/www/resetdefabrica/html
    # Configurar permisos
    chown -R www-data:www-data /var/www/resetdefabrica/html
    chmod -R 755 /var/www/resetdefabrica
    # Copiar archivo de configuración de Nginx desde la máquina anfitriona a la máquina virtual
    cp -v /vagrant/resetdefabrica /etc/nginx/sites-available/resetdefabrica
    ln -fs /etc/nginx/sites-available/resetdefabrica /etc/nginx/sites-enabled/
    systemctl restart nginx

    # Configurar vsftpd
    mkdir -p /home/danielgf/ftp

    # Generar certificados SSL
    sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt

    SHELL
  end