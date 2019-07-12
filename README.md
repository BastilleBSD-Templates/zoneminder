# zoneminder
Bastille Template for Zoneminder CC TV Application

The template should install the AMP stack on your Freebsd for you. Now you need to
manually configure a few things.

Now using vim open the Apache config file for writing:
	
	# vim  /usr/local/etc # nano apache24/httpd.conf

Add the following to the end of the file:

 Code:

	<IfModule dir_module>
		DirectoryIndex index.php index.html
		<FilesMatch "\.php$">
			SetHandler application/x-httpd-php
		</FilesMatch>
		<FilesMatch "\.phps$">
			SetHandler application/x-httpd-php-source
		</FilesMatch>
	</IfModule>i

Now edit /usr/local/etc/php.ini and uncomment the timezone line and correct it to your
timezone:

	date.timezone = America/Denver


Now we configure the core ZM elements, starting with initialising the ZM database:

	# mysql -uroot -p < /usr/local/share/zoneminder/db/zm_create.sql

	mysql -uroot -p -e "grant select,insert,update,delete,create,alter,index,lock \
	tables on zm.* to 'zmuser'@localhost identified by 'zmpass';"

	# chmod 740 /usr/local/etc/zm.conf
	# chmod 740 /usr/local/my.cnf

Edit /usr/local/etc/apache24/httpd.conf and append the following:

	<VirtualHost *:80>
		ServerName zm.freenas.local
		ServerAdmin email@example.com
		DocumentRoot "/usr/local/www/zoneminder/"
		<Directory "/usr/local/www/zoneminder">
			Options FollowSymLinks
			AllowOverride All
		Require all granted
		</Directory>
		ScriptAlias /cgi-bin "/usr/local/www/zoneminder/cgi-bin"
		<Directory "/usr/local/www/zoneminder/cgi-bin">
			Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
			AllowOverride All
		Require all granted
		</Directory>
		LogLevel debug
	</VirtualHost>



Open your ip in a web browser and see the ZM main page.

Now secure your installation with 

	# mysql_secure_installation



