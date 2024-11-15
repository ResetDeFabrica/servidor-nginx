# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "debian/bullseye64"
    config.vm.network "private_network", ip: "192.168.33.10"


    config.vm.provision "shell", inline: <<-SHELL

    # Actualizar repositorios e instalar Nginx
        apt-get update
        apt-get install -y nginx git vsftpd openssl
        systemctl status nginx

    # Crear carpetas del sitio web
    mkdir -p /var/www/resetdefabrica/html
    git clone https://github.com/cloudacademy/static-website-example /var/www/resetdefabrica/html
    # Configurar permisos
    chown -R www-data:www-data /var/www/resetdefabrica/html
    chmod -R 755 /var/www/resetdefabrica

    # Crear directorio para la tarea 5 de mi sitio web personal
    mkdir -p /var/www/miwebpersonal/html
    git clone https://github.com/ResetDeFabrica/servidor-nginx /var/www/miwebpersonal/html
    # Configurar permisos
    chown -R vagrant:www-data /var/www/miwebpersonal/html 
    chmod -R 755 /var/www/miwebpersonal/html

    # Copiar archivo de configuración de Nginx desde la máquina anfitriona a la máquina virtual
    cp -v /vagrant/resetdefabrica /etc/nginx/sites-available/resetdefabrica
    cp -v /vagrant/miwebpersonal /etc/nginx/sites-available/
    ln -s /etc/nginx/sites-available/resetdefabrica /etc/nginx/sites-enabled/
    ln -s /etc/nginx/sites-available/miwebpersonal /etc/nginx/sites-enabled/

    # Configuración de FTPS
    # Crear carpeta FTP para el usuario "vagrant" y dentro miwebpersonal
    mkdir -p /home/vagrant/ftp
    mkdir -p /home/vagrant/ftp/miwebpersonal

    # Dar permisos al usuario vagrant y miwebpersonal
    chown -R vagrant:vagrant /home/vagrant/ftp
    chmod 755 /home/vagrant/ftp
    chown -R vagrant:vagrant /home/vagrant/ftp/miwebpersonal
    chmod -R 755 /home/vagrant/ftp/miwebpersonal
    cp -v /vagrant/index.html /home/vagrant/ftp/miwebpersonal



    # Añadir contraseña para usuario vagrant
    echo "vagrant:vagrant" | chpasswd

    # Generar certificados SSL
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt -subj "/C=ES/ST=State/L=City/O=Organization/OU=Unit/CN=example.com"

    # Configurar vsftpd.con con la configuración nueva
    cp -v /vagrant/vsftpd_nueva.conf /etc/vsftpd.conf

    # Reiniciar servicios
    systemctl restart nginx
    systemctl restart vsftpd
    SHELL
  end