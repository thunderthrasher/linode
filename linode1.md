# Basic Nginx, PHP and MYSQL Installation on Linode
MySql PHP and "myinfofile.php"

NGINX is by far the fastest and most secure reverse open sorse reverce proxy server out there. Granted it is not the easiest to configure and set up, but the speed makes it well worth the learning curve. I managed to get a 58% increase mooving from Aparche to NGINX which for me was the WOW Factor. 

Prerequisits : 
1[A linode account](https://www.linode.com/)
12 A SSH Client ie. [Putty (open Source)](http://www.putty.org/)

## **In this tutorial you will learn **

		1	How to set up how to install ubuntu 14.04
        2	Install the NGIX webserver on a Server Block AKA Virtual Host
        3	install & Seup MySql
        4	Install & Setup PHP (FastCGI and MYQLL modules)
        5	And finally see your "Hello World" program in action

...Lets get started...

### 1. How to set up how to install ubuntu 14.04

Once you have created an account simply click on Deploy a Linux Distribution, from the dropdown menu which follows, choose:

		Ubuntu 14.04 LTS, Swap 512, and choose a password (This password will be used for your ssh root login.
   
   
![](http://thunderthrasher.com/deploy.png)

Once Ubuntu is fully installed, you will need to boot your system, simply select your just created Linode, and click on the Boot button.

	After aboyt 5 to 10 seconds your webbrowser will show you this :
    
![](http://thunderthrasher.com/installed.png)
        

### 2.1. Install the NGIX webserver on a Server Block AKA Virtual Host

Statt off by going yo uout linode desktop and clicking on the just created My "Linodes" link.  (Just under your deshboard tab.)

1. Highlite and copy the IP (Crtl-C)...writing it down works too :-)
2. Start up your SSH Client (Putty), and paste this IP into the "Host name (or IP adress) Field", for this example I have used **96.126.113.121** 
3. You may get a dialog box of **PuTTY Security Alert** (You may ignore it (if you wish) and click on the Accept button) ,*basically it is a notice that you have logged on ffor the first time from your computer*
4. Next a dialog window will open **Login as:** type in root
5. After this It will as you for **root@96.126.113.121's password:** type in the password you used while setting up the Ubuntu 14.04 (Step 1 of this Tutorial).

![putty](http://thunderthrasher.com/putty1.png)

All you have to do now is to type:

1. `sudo apt-get update` Although Linode keeps their Repos it's is always a good idea to do this extra step of downloading the package lists therefore updating the repositories.
2. `sudo apt-get install nginx` and Vuala, a few seconds later Nginx is installed.

As Nginx is configured to start up upon installation, you can go streight to your browser and type in your IP adress as `http://96.126.117.111/`

	If all went well, you should see a Welcome to Linux page.
    
![](http://thunderthrasher.com/nginx1.png)
   
#3. Installing & Setting up MySQL

1. As we just updated the repos, there is no need to do ot again, you may now type `sudo apt-get install mysql-server` To install the MYSQL server, in this step you will need to create a root password MYSQL **root** Password.  
2. Then `sudo mysql_install_db`, sothat the directory structure is generated.
3. Security is important, therefore we will type `sudo mysql_secure_installation`. Enter your root Password that you created above, You may now change some of your security settings. (It is a way of forcing users to be more aware of their settings)

###2.2. It is now time to configure MySQL 

1.  Log into your MYSQL Admin account by typing `mysql -u root -p` (type in your MYSQL root password that you created earlier)
2.  You are now in the MYSQL admin, this can be seen as your prompt starts with **mysql>** Create a Database `CREATE DATABASE mydatabase;` *remember to use the **;** (Semi-colon) after every mysql statement in the admin*
3.  Create a new User `CREATE USER myuser@localhost IDENTIFIED BY 'mypassword';` (Replace *myuser* and *mypassword* with your own).
4.  Assign priviliges yo your new user `GRANT ALL PRIVILEGES ON mydatabase.* TO myuser@localhost;`
4.  If you are happy with these settings type `FLUSH PRIVILEGES;` and save the changes to disk.
5.  In order to test your database, list the users by typing ` select host, user, password from mysql.user;` or list all the databases `show databases;`
6.  You may now exit the Database admin knowing that your user, database and priviliges have been configured by typing `exit;`

##4. Setup PHP##

1. `sudo apt-get install php5-fpm php5-mysql` will install PHP and the MYSQL modules.
2. Now that PHP is installed you can create your first php file by typing `echo '<?php phpinfo(); ?>' > /usr/share/nginx/html/myinfofile.php`. This will automatically write the text within the single quotation marks, to the file stipulated after the greater than sign.
3. You now have to instruct NGINX to accept and execute PHP files. this is done by editing the default server block. Please type `sudo nano /etc/nginx/sites-available/default`
3.  nano (a linux text editor will appear), Find the line **index index.html index.htm;** and add `index.php` sothat the full line looks like **index index.php index.html index.htm;**. 
4.  You will also need to add these lines: under the location / block
`    	
		location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    	}
        
4.  After all is said and done your entire **/etc/nginx/sites-available/default** should look like this:
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


5.  **OPTIONAL** If you have a domain ie example.com you may replace localhost with your domain name as `server_name www.example.com example.com;`
6.  After this press Ctrl-X, nano will then promt **Save Modified Buffer?** click **Y** and ENTER. (unless yoy do not wish to save your hard work :-)
7.  Now all that you need to do is to restart the ngix server by typing `sudo service nginx restart`, there is really no need to restart the php server as we did not change any of its configuration files, but just in case, that command would be `sudo service php5-fpm restart` yu could also just type `reboot`, to reboot the entire server from the SSH terminal.

Once you are ready you can go to your domain `http://www.example.com/myinfofile.php ` or your IP adress as `http://96.126.113.121/myinfofile.php`, to see your php server in action. 
		
       	The page should look like this: 
![](http://thunderthrasher.com/phpini.png)

This is a Linode Community guide by author “Daniel Rosenauer".











