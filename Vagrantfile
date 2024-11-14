# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "debian/bullseye64"
    config.vm.network "private_network", ip: "192.168.56.10"
    config.vm.provision "shell", inline: <<-SHELL

    # Actualizar repositorios e instalar Nginx
        apt-get update
        apt-get install -y nginx
        systemctl status nginx

    # Crear carpetas del sitio web
    mkdir -p /var/www/resetdefabrica_web/html
    cd /var/www/resetdefabrica_web/html
    git clone https://github.com/cloudacademy/static-website-example
    # Configurar permisos
    chown -R www-data:www-data /var/www/resetdefabrica_web/html
    chmod -R 755 /var/www/resetdefabrica_web

    ln -fs /etc/nginx/sites-available/resetdefabrica_web /etc/nginx/sites-enabled/
    systemctl restart nginx

    # Instalar y configurar vsftpd
    # apt install -y vsftpd openssl
    # mkdir -p /home/vagrant/ftp

    # Generar certificados SSL
    # sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt -subj "/C=ES/ST=Madrid/L=Madrid/O=MiOrganizacion/OU=IT/CN=resetdefabrica_web"

    SHELL
  end