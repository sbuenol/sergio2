# sergio2
URL del repositorio de GitHub donde se ha alojado el documento técnico escrito en Markdown:

https://github.com/sbuenol/sergio2.git

Actualizamos el sistema y los paquetes instalados con:

`sudo apt-get update`
`sudo apt-get upgrade`

Instalamos el servicio de ssh, apache, MySQL , php y la libreria del php con:

`sudo apt-get install (nombre del paquete)`

nombres de paquetes: ssh, apache2, mysql-server, php-mysql y libapache2-mod-php

Iniciamos los servicios anteriores con:

`sudo systemctl start  (nombre del paquete)`

Accedemos a /var/www/html, creamos adminer y entramos en él con:

`cd /var/www/html`
`sudo mkdir adminer`
`cd admimner`

Una vez dentro añadimos los siguientes enlaces de instalación con:

`sudo wget https://github.com/vrana/adminer/releases/download/v4.7.3/adminer-4.7.3-mysql.phpdow`
`sudo mv adminer-4.7.3-mysql.php index.php`

Instalamos un analizador de logs para Apache Server(GoAcces) y lo actualizamos con:

`echo "deb http://deb.goaccess.io/ $(lsb_release -cs) main" | sudo tee -a /etc/apt/sources.list.d/goaccess.list`
`wget -O - https://deb.goaccess.io/gnugpg.key | sudo apt-key add -sudo apt-get update `
`sudo apt-get install goaccess`

Ahora podemos usarla para procesar los archivos de log "access.log" de Apache HTTP Server, muestra la información del log en tiempo real en el terminal en modo texto con:

`goaccess /var/log/apache2/access.log -c`

Con los siguiente comandos parseamos el archivo de log "access.log" y genera un archivo HTML estático o en tiempo real:

estático: `goaccess /var/log/apache2/access.log -o /var/www/html/report.html --log-format=COMBINED`

tiemporeal: `goaccess /var/log/apache2/access.log -o /var/www/html/report.html --log-format=COMBINED --real-time-html`

Para controlar el acceso a un directorio con .htaccess creamos un directorio (stats) dentro del directorio /var/www/html donde se podrán consultar los informes generados con goaccess, el acceso a este directorio deberá estar controlado y solo se podrá acceder mediante un usuario(sergio) y una contraseña, lo hacemos con:

`mkdir /var/www/html/stats`
`sudo goaccess /var/log/apache2/access.log -o /var/www/html/stats/index.html --log-format=COMBINED --real-time-html &`
`sudo htpasswd -c /home/sergio/.htpasswd sergio`

Creamos el archivo .htaccess en el directorio que queremos proteger con usuario y contraseña(/var/www/html/stats/.htaccess):

`sudo nano /var/www/html/stats/.htaccess`

El contenido del archivo ".htaccess":

`AuthType Basic`

`AuthName "Restricted Content"`

`AuthUserFile /home/usuario/.htpasswd`

`Require valid-user`

Editamos el archivo configuración de Apache:

`sudo nano /etc/apache2/sites-enabled/000-default.conf`

Añadimos la siguiente sección dentro de las etiquetas de 
`<VirtualHost *:80>` y `</VirtualHost>`:

`<Directory "/var/www/html/stats">` 
  
  `Options Indexes FollowSymLinks`
  
  `AllowOverride All`
  
  `Require all granted`

`</Directory>`
