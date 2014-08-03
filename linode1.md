# Basic Nginx, PHP and MYSQL Installation on Linode (LEMP)


The NGINX web server is a fast, lightweight server designed to efficiently handle the needs of both low and high traffic websites. Granted it is not the easiest to configure and set up, but the speed makes it well worth the while and the *learning curve*. I myself managed to get a 58% speed increase by moving from Apache to NGINX which was real **WOW Factor** for me . 

This guide will help you get NGINX up and running on your Linode VPS.

Prerequisites :
-------------

1. A Linode account (https://www.linode.com/).
2. A SSH Client ie. PuTTY (http://www.putty.org/).

In this tutorial you will learn
--------------------------------

	1. How to set up & install Ubuntu 14.04 on Linode.
	2. Install the NGINX webserver on the default Server Block AKA Virtual Host.
	3. Install & Setup MySql.
	4. Install & Setup PHP (FastCGI and MYSQL modules).
	5. And finally see your **myinfofile.php** (phpinfo) program in action.


## ...Let's get started... (It'll be FUN !!!)


### 1. How to set up & install Ubuntu 14.04

Once you have created a Linode account simply click on **Deploy a Linux Distribution** in your Linode Dashboard. You will then be redirected to the Linode Deploy page. Once there, choose the following from the dropdown menu:

	Ubuntu 14.04 LTS, Swap 512, and choose a password (This password will be used for your ssh
    root login.
    
![](http://thunderthrasher.com/deploy.png)

Once Ubuntu is fully installed, you will need to boot your system, just select your just created Linode, and click on the **Boot button**.

	After about 5 to 10 seconds your webbrowser will show you this :

![](http://thunderthrasher.com/installed.png)
        

### 2.1. Install the NGIX webserver on the default Server Block AKA Virtual Host

{: .Please Note: }
> 
> I will be using the IP **96.126.113.121** as an example IP throughout this tutorial.

Start off by logging into your Linode account, proceeding to your dashboard and click on the *Linodes* link (Just under your dashboard tab).

	1.  Highlight and copy the IP (Crtl-C)...writing it down works too :-) .
	2.  Start up your SSH Client (Putty), and paste this IP into the **Host name (or IP address) 
    	Field**.
    3.  You may get a dialog box displaying **PuTTY Security Alert** (You may ignore it if you wish
    	and click on the Accept button) ,*basically it's a notice that this is the first time that
        you logged in from your current computer*.
	4.  Next a dialog window will open, displaying **Login as:** type in `root`.
	5.  After this Linode will ask you for **root@96.126.113.121's password:** type in the password 
    	you used while setting up the Ubuntu 14.04 **(Step 1 of this Tutorial)**.

![putty](http://thunderthrasher.com/putty1.png)

All you have to do now is to type:

1. `sudo apt-get update` Although Linode keeps their Repo's up to date, it's is always a good idea to do this extra step of downloading the package lists therefore updating the repositories.
2. `sudo apt-get install nginx` and Voilà, a few seconds later NGINX is installed.

As NGINX is configured to start up upon installation, you can go straight to your browser and type in your IP adress as `http://96.126.113.121/`

	If all went well, you should see a Welcome to Linux page.
    
![](http://thunderthrasher.com/nginx1.png)
   
### 3. Installing & Setting up MySQL

1. As we just updated the repos, there is really no need to do it again, you may now type `sudo apt-get install mysql-server` to install the MYSQL server, in this step you will need to create a root password MYSQL **root** Password.
2. Then `sudo mysql_install_db`, so that the directory structure is generated.
3. Security is important, therefore we will type `sudo mysql_secure_installation`. Enter your root Password that you created above. You may now change some of your security settings if you wish. (It is a way of forcing users to be more aware of their settings)


### 3.1. It is now time to configure MySQL

1. Log into your MYSQL Admin account by typing `mysql -u root -p` (then type in your MYSQL root password that you created earlier)
2. You are now in the MYSQL admin, this can be seen as your prompt starts with **mysql>**
3. Create a new database by typing `CREATE DATABASE mydatabase;` remember to use the ** ;** (Semi-colon) after every mysql statement in the admin.
4. Create a new user `CREATE USER myuser@localhost IDENTIFIED BY 'mypassword';` (Replace *myuser* and *mypassword* with your own).
5. Assign privileges to your new user `GRANT ALL PRIVILEGES ON mydatabase.* TO myuser@localhost;`
6. If you are happy with these settings type `FLUSH PRIVILEGES;` *this command essentially saves the changes*.
7. In order to test your database, list all the users by typing `select host, user, password from mysql.user;` or list all the databases `show databases;`.
8. You may now exit the Database admin rest assured in the knowledge, that your user, database and privileges have been created and configured. Please type `exit;`

### 4. Setup PHP

1. `sudo apt-get install php5-fpm php5-mysql` will install PHP with the FastCGI and the MYSQL modules.
2. Now that PHP is installed you can create your first php file by typing `echo '<?php phpinfo(); ?>' > /usr/share/nginx/html/myinfofile.php`. This will automatically write the text within the single quotation marks, to the file stipulated after the *greater than* sign.
3. You now have to instruct NGINX to accept and execute PHP files. This is done by editing the default server block. Please type `sudo nano /etc/nginx/sites-available/default`
4. Nano (a linux text editor) will appear. Find (Crtl-W) the line **index index.html index.htm;** and add `index.php` sothat the full line looks like **index index.php index.html index.htm;**.
5. You will also need to add these lines: under the location / block
    	
			location ~ \.php$ {
        	try_files $uri =404;
        	fastcgi_split_path_info ^(.+\.php)(/.+)$;
        	fastcgi_pass unix:/var/run/php5-fpm.sock;
        	fastcgi_index index.php;
        	include fastcgi_params;
    		}
        
6.  After all is said and done, your entire **/etc/nginx/sites-available/default** should look like this:

		server {
    		listen 80 default_server;
    		listen [::]:80 default_server ipv6only=on;
    		root /usr/share/nginx/html;
    		index index.php index.html index.htm;
    		server_name localhost;
		location / {
        	try_files $uri $uri/ =404;
    		}
    	}
    	location ~ \.php$ {
        	try_files $uri =404;
        	fastcgi_split_path_info ^(.+\.php)(/.+)$;
        	fastcgi_pass unix:/var/run/php5-fpm.sock;
        	fastcgi_index index.php;
        	include fastcgi_params;
	    	}
		}
        
7.  **OPTIONAL** If you have a domain ie. *www.example.com* you may replace *localhost* with your domain name as `server_name www.example.com example.com;`
8.  After this press Ctrl-X, nano will then prompt you **Save Modified Buffer?** click **Y ** and ENTER. (unless you don't want to save your hard work :-) **Crtl-Z will exit nano without saving your file**
9.  Now all that you need to do is to restart the NGINX server by typing `sudo service nginx restart`, there is really no need to restart the php server as we did not change any of it's configuration files, but just in case, the command for that would be `sudo service php5-fpm restart`. You could also just type `reboot`, to reboot the entire server from the SSH terminal, however this may take 15 seconds or so. 

Once you are ready you can go to your domain `http://www.example.com/myinfofile.php ` or your IP address as `http://96.126.113.121/myinfofile.php`, to see your php server in action. I do recommend removing the **myinfofile.php** file on your server once done, as it does pose a security risks (you may remove it via `rm /usr/share/nginx/html/myinfofile.php` and recreate it when needed by typing `echo '<?php phpinfo(); ?>' > /usr/share/nginx/html/myinfofile.php`.
		
       	The page should look like this:
        
![](http://thunderthrasher.com/phpini.png)

This is a Linode Community guide by author **Daniel Rosenauer**.
