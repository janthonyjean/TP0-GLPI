# TP0-GLPI
lien d'installation de glpi
Installer GLPI sous débian 13

su -c "apt install sudo -y"
su -c "/usr/sbin/usermod -aG sudo anthony"

Installation des packages nécéssaire

sudo apt update && sudo apt upgrade -y
sudo apt install apache2 mariadb-server php -y

Installer Php
sudo apt install php-fpm -y
sudo apt install php-{mysql,mbstring,curl,gd,xml,intl,ldap,apcu,xmlrpc,zip,bz2,bcmath} -y

Préparation de la base de donnée
sudo mariadb
create database glpi_npt;
grant all privileges on glpi_npt.* to anthony@localhost identified by "dorice";

Téléchargement de GLPI
cd /tmp
wget https://github.com/glpi-project/glpi/releases/download/11.0.9/glpi-11.0.9.tgz

sudo tar -xvzf glpi-11.0.9.tgz -C /var/www/html
sudo chown -R www-data /var/www/html

Configuration du service web
php -v
sudo nano /etc/apache2/sites-available/glpi.conf

<VirtualHost *:80>
    ServerName glp11-neptunet
    ServerAlias 192.168.0.99
    DocumentRoot /var/www/html

    Alias /glpi /var/www/html/glpi/public

    <Directory /var/www/html/glpi/public>
        Options -Indexes +FollowSymLinks
        Require all granted
        RewriteEngine On
        RewriteBase /glpi/
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule ^ index.php [QSA,L]
    </Directory>

    <FilesMatch \.php$>
        SetHandler "proxy:unix:/run/php/php8.4-fpm.sock|fcgi://localhost"
    </FilesMatch>

    ErrorLog ${APACHE_LOG_DIR}/glpi_error.log
    CustomLog ${APACHE_LOG_DIR}/glpi_access.log combined
</VirtualHost>

sudo a2enmod proxy_fcgi setenvif
sudo a2enmod rewrite
sudo a2enconf php*-fpm
sudo a2dissite 000-default.conf
sudo a2ensite glpi.conf
sudo systemctl restart apache2

Installation de GLPI



