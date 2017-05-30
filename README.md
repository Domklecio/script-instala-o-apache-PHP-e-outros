# script-instala-o-apache-PHP-e-outros

Aluno: Klecio Holanda de Sousa,

Script de instalação Apache e PHP

#!/bin/bash

tabs 4
clear

#----- Import Functions -----#

dir="$(dirname "$0")"

#. $dir/functions/phpubunto
#. $dir/functions/cleanup
#. $dir/functions/protubunto
#. $dir/functions/configure
#. $dir/functions/nginx
#. $dir/functions/apache2
#. $dir/functions/phpcentos
#. $dir/functions/protcentos
#. $dir/functions/update
#. $dir/functions/host

#----- Fancy Messages -----#
show_error(){
echo -e "\033[1;31m$@\033[m" 1>&2
}
show_info(){
echo -e "\033[1;32m$@\033[0m"
}
show_warning(){
echo -e "\033[1;33m$@\033[0m"
}
show_question(){
echo -e "\033[1;34m$@\033[0m"
}
show_success(){
echo -e "\033[1;35m$@\033[0m"
}
show_header(){
echo -e "\033[1;36m$@\033[0m"
}
show_listitem(){
echo -e "\033[0;37m$@\033[0m"
}

# Main
function main {
eval `resize`
    MAIN=$(whiptail \
        --notags \
        --title "Script Apache2 / php" \
        --menu "\nEscolha a opcao desejada" \
        --cancel-button "Quit" \
        $LINES $COLUMNS $(( $LINES - 12 )) \
        update     'Update' \
        apache2    'Instalar apache UBUNTO' \
        phpubunto  'Instalar php UBUNTO' \
        protubunto 'Instalar pagina protegida apache UBUNTO' \
        cleanup   'Criar hosts'\
        3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus = 0 ]; then
$MAIN
else
quit
fi
}

#update
function update {
if (whiptail --title "System Update" --yesno "Proceed with system update?" 10 60) then
    # Update repository information
    show_info 'Updating repository information...'
    show_warning 'Requires root privileges'
    sudo apt-get update
    show_success 'Done.'
    # Dist-Upgrade
    show_info 'Performing system update...'
    sudo apt-get dist-upgrade -y
    # Done
    show_success 'Done.'
    # Check
    EXITSTATUS=$1
    if [[ $EXITSTATUS != 0 ]]; then
        # Already up-to-date
        show_success 'Already up-to-date.'
        whiptail --title "Finished" --msgbox "Update atualizado" 8 78
        main
    else
        whiptail --title "Finished" --msgbox "System update complete" 8 78
        main
    fi
else
    main
fi
}

# upgrade
function upgrade {
    
       sudo apt-get upgrade
 }

# repositorio
function repositorio {
    
		echo " ## Arquivo Sources.list

# Atualizações de Segurança
# Security updates
deb http://security.debian.org/ jessie/updates main contrib non-free
deb-src http://security.debian.org/ jessie/updates main contrib non-free

## Espelho Debian

# Base de dados do repositório
# Base repository
deb http://ftp.br.debian.org/debian jessie main contrib non-free
deb-src http://ftp.br.debian.org/debian jessie main contrib non-free

# Atualizações estáveis
# Stable updates
deb http://ftp.br.debian.org/debian jessie-updates main contrib non-free
deb-src http://ftp.br.debian.org/debian jessie-updates main contrib non-free

# backports estáveis
# Stable backports
deb http://ftp.debian.org/debian jessie-backports main contrib non-free
deb-src http://ftp.debian.org/debian jessie-backports main contrib non-free

# Atualizações Propostas
deb http://ftp.debian.org/debian jessie-proposed-updates main contrib non-free
" >/etc/apt/sources.list
	sudo apt-get update

		
 }
# instalação apache ubunto
function apache2 {
    
       
		apt-get install apache2	
		touch /var/www/html/index.html
		echo "Meu Servidor" >/var/www/html/index.html
		sudo systemctl restart apache2
		sudo systemctl status apache2
		
		wget http://127.0.0.1/	
 }
 #instalação PHP no ubunto
 function phpubunto {
   
				
		#sudo apt-get install mysql-server apache2 libapache2-mod-php5 php5 php5-mysql phpmyadmin 
		sudo apt-get install php5 libapache2-mod-php5 php5-mcrypt
		cd /etc/apache2/mods-enabled/
		rm dir.conf
		touch /etc/apache2/mods-enabled/dir.conf
		sudo service apache2 restart
		
		echo "<IfModule mod_dir.c>
		DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
		</IfModule>" >>/etc/apache2/mods-enabled/dir.conf


		sudo service apache2 restart
		apt-cache search php5-
		apt-cache show php5-cli
		sudo apt-get install php5-cl
		touch /var/www/html/info.php
		echo "Meu Servidor PHP<?php
		phpinfo();
		?>" >>/etc/apache2/mods-enabled/dir.conf/var/www/html/info.php

		wget http://127.0.0.1/info.php
		
		
			
 }
  #Proteger Sites Ubunto
 function protubunto {
		sudo apt-get update
		sudo apt-get install apache2-utils
       
		sudo htpasswd -c /etc/apache2/.htpasswd Dom 	
		sudo htpasswd /etc/apache2/.htpasswd Convidado
		cat /etc/apache2/.htpasswd
		cd /etc/apache2/sites-enabled
		rm -f 000-default.conf
		touch 000-default.conf
		echo "<VirtualHost *:80>
		ServerAdmin webmaster@localhost
		DocumentRoot /var/www/html
		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined

		<Directory "/var/www/html">
		AuthType Basic
		AuthName "Restricted Content"
		AuthUserFile /etc/apache2/.htpasswd
		Require valid-user
		</Directory>
		</VirtualHost>" >>/etc/apache2/sites-enabled/000-default.conf

		sudo apache2ctl configtest
		sudo systemctl restart apache2
		sudo systemctl status apache2
		

		echo "<Directory /var/www/>
		Options Indexes FollowSymLinks
		AllowOverride All
		Require all granted	
		</Directory>>" >>/etc/apache2/apache2impress.conf

		sudo gedit /etc/apache2/apache2impress.conf
		sudo gedit /etc/apache2/apache2.conf 
		sudo systemctl restart apache2
		sudo systemctl status apache2 


		cd /var/www/html/
		rm -f .htaccess
		touch .htaccess
		echo "AuthType Basic
		AuthName "Restricted Content"
		AuthUserFile /etc/apache2/.htpasswd
		Require valid-user" >>/var/www/html/.htaccess

		sudo systemctl restart apache2
		sudo systemctl status apache2 

		cd /root		
		#https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-apache-on-ubuntu-16-04
			
 }
 #crias mais Hosts
function  cleanup { 

		sudo apt-get install apache2

		touch /var/www/html/index.html
		echo "Meu Servidor" >/var/www/html/index.html
		sudo systemctl restart apache2
		sudo systemctl status apache2
		
    	cd /etc/apache2/sites-available
		
		sleep 10
		sudo mkdir /var/www/html/site1/index.html
		cd /var/www/html/site1
		sudo chown -R $USER:$USER /var/www/html/site1/index.html
		
		html/site1/index.html
		
		sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/ 001-siteKlecio.conf
		
		echo "<VirtualHost *:80>
			ServerAdmin elkleciano@hotmail.com
			ServerName siteklecio.com.br	
			ServerAlias www.siteklecio.com.br
			DocumentRoot /var/www/example.com/public_html
			ErrorLog ${APACHE_LOG_DIR}/error.log
			CustomLog ${APACHE_LOG_DIR}/access.log combined
			</VirtualHost>" >>/etc/apache2/sites-enabled/001-siteKlecio.conf
		
		echo "site klecio" > index.html

		sudo a2ensite 001-siteklecio.conf

		echo "127.0.0.1	localhost siteklecio.com.br www.siteklecio.com.br" >>/etc/hosts
		service apache2 reload		
		ping www.siteklecio.com.br
		
		#https://www.digitalocean.com/community/tutorials/como-configurar-apache-virtual-hosts-no-ubuntu-14-04-lts-pt

}
# Quit
function quit {
if (whiptail --title "Quit" --yesno "Are you sure you want quit?" 10 60) then
echo "Exiting..."
show_info 'Thanks for using!'
exit 99
else
main
fi
}

#RUN
check_dependencies
while :
do
main
done
#FIM DO SCRIPT
