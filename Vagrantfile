# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "debian/bullseye64"
    config.vm.network "private_network", ip: "192.168.56.10"
    config.vm.provision "shell", inline: <<-SHELL

    # Actualizar repositorios e instalar Nginx
        apt-get update
        apt-get install -y nginx

    # Crear carpetas del sitio web
    mkdir -p /var/www/resetdefabrica_web/html
    cd /var/www/resetdefabrica_web/html
    git clone https://github.com/cloudacademy/static-website-example
    # Configurar permisos
    chown -R www-data:www-data /var/www/resetdefabrica_web/html
    chmod -R 755 /var/www/resetdefabrica_web

    SHELL
  end