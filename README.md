# Práctica de Instalación y configuración de servidor webNginx

Todo el trabajo realizado y el contenido de la práctica se encuentra aqui.

### Enlace al repositorio

https://github.com/ResetDeFabrica/servidor-nginx

# Archivo vagrant

```
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

```

# Instalación servidor web Nginx

## Actualizar e instalar nginx

```
apt-get update
apt-get install -y nginx

```

## Comprobar el estado de nginx

```
systemctl status nginx

```
# Configuarción de Sitios Web

1. Creación de Carpetas
```
mkdir -p /var/www/resetdefabrica/html
mkdir -p /var/www/miwebpersonal/html

```
2. Configuración de Permisos
```
chown -R www-data:www-data /var/www/resetdefabrica/html
chmod -R 755 /var/www/resetdefabrica
chown -R vagrant:www-data /var/www/miwebpersonal/html 
chmod -R 755 /var/www/miwebpersonal

```

3. Configuración de Nginx

Archivo de configuración de Nginx para resetdefabrica y miwebpersonal:

```
resetdefabrica

server {
    listen 80;
    listen [::]:80;
    root /var/www/resetdefabrica/html;
    index index.html index.htm index.nginx-debian.html;
    server_name resetdefabrica.local;
    location / {
        try_files $uri $uri/ =404;
    }
}

miwebpersonal

server {
    listen 80;
    listen [::]:80;
    root /var/www/miwebpersonal/html;
    index index.html index.htm index.nginx-debian.html;
    server_name miwebpersonal.local;
    location / {
        try_files $uri $uri/ =404;
    }
}

```

![capturadepantalla](capturavagrant.JPG)

# Configuración del Servidor FTPS

1. Instalación de vsftpd
```
apt-get install -y vsftpd
```
2. Configuración de SSL
```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt
```
3. Configuración de vsftpd

- Archivo vsftpd_nueva.conf:

```
listen=YES
listen_port=21
# listen_ipv6=NO
anonymous_enable=NO
local_enable=YES
dirmessage_enable=YES
use_localtime=YES
xferlog_enable=YES
connect_from_port_20=YES
secure_chroot_dir=/var/run/vsftpd/empty
pam_service_name=vsftpd

rsa_cert_file=/etc/ssl/certs/vsftpd.crt
rsa_private_key_file=/etc/ssl/private/vsftpd.key
ssl_enable=YES
allow_anon_ssl=NO
force_local_data_ssl=YES
force_local_logins_ssl=YES
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO
require_ssl_reuse=NO
ssl_ciphers=HIGH
local_root=/home/vagrant/ftp

implicit_ssl=NO
write_enable=YES
local_umask=022

ftpd_banner=Welcome to FTPS Server
chroot_local_user=YES
allow_writeable_chroot=YES
pasv_min_port=10000
pasv_max_port=10100
secure_chroot_dir=/var/run/vsftpd/empty

```

# Verificación y Pruebas

1. Pruebas Web
- Accede a tu sitio web:

http://resetdefabrica.local

![capturadepantalla](/imagenes/Conexionpaginaclonadadegit.jpg)

2. Pruebas FTPS
Conexión mediante FileZilla:

- Host: 192.168.33.10
- Puerto: 21
- Usuario: vagrant
- Contraseña: vagrant

3. Miwebpersonal

- He creado un archivo index.html en la carpeta ftp/miwebpersonal

![capturadepantalla](/imagenes/DNS-de-miwebpersonal-en-la-MV.jpg)
![capturadepantalla](/imagenes/miwebpersonal-index.html.jpg)

**Nota:** El archivo index.html se crea en la carpeta ftp/miwebpersonal, pero no en la carpeta html de miwebpersonal.
Me ha costado mucho tiempo buscar la solución, no se si está bien pero desde filezilla se pueden ver los archivos y te puedes conectar.

4. Imagenes del proceso

- Access.log

![capturadepantalla](/imagenes/Comprobacionaccess.log.jpg)

- Error.log

![capturadepantalla](/imagenes/Comprobacionerror.log.jpg)

- Cambio de permisos DNS

![capturadepantalla](/imagenes/CambiodeDNScorrecto.jpg)

- Certificado desconocido en filezilla

![capturadepantalla](/imagenes/Certificadodesconocido.jpg)

- Conexión con vagrant

![capturadepantalla](/imagenes/Conexionvagrant.jpg)

- Conexión exitosa con vagrant

![capturadepantalla](/imagenes/Conexionexitosavagrant.jpg)

# Cuestiones Finales

## ¿Qué pasa si no hago el link simbólico entre sites-available y sites-enabled de mi sitio web?
Si no creas el enlace simbólico, Nginx no cargará la configuración del sitio web. Los archivos en sites-available son solo configuraciones disponibles, pero no activas. Nginx solo lee las configuraciones en sites-enabled.

## ¿Qué pasa si no le doy los permisos adecuados a /var/www/nombre_web?
Si no das los permisos adecuados, Nginx no podrá leer los archivos del sitio web, lo que resultará en un error 403 (Forbidden) cuando intentes acceder al sitio. Es importante que el usuario www-data (el usuario bajo el cual se ejecuta Nginx) tenga permisos de lectura y ejecución en los directorios, y permisos de lectura en los archivos.