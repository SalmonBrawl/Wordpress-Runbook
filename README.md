# Wordpress RunBook

## Hello friends, here I will be showing you how to fully install wordpress onto your web server. We will be using Ubuntu 16.04, PHP7.2 and Mysql5.7. For today "..." symbolizes text that is not important to our current goals

### The first step is to SSH into your server.  
1. To SSH into your server you need to first find your server's ip address as well as collect the username and password of the server.

2. Next you will run the command "ssh username@ipAddress" if your SSH has been modified to a different port then you may include that with "-p portNumber" however standard SSH is through port 22.

3. It may prompt you for a password, you will then enter your user's password.

4. Confirm you are in your webserver by running "ifconfig" it should return that you have opened a shell in the webserver.

For example's sake, our IP adress will be 10.10.10.10, our username will be "U" and our password will be "Pa$$".

```sh
C:\Users\SalmonBrawl> ssh U@10.10.10.10  
U@10.10.10.10's password:Pa$$  
Welcome to _Ubuntu 10.04.6_ LTS...   
U@ubuntu:~$ ifconfig  
... inet addr:10.10.10.10...  
```

### The next phase will involve moving to your desired webserver path and granting permissions to your user.

1. Navigate to your webserver's file path. Remember, the shell is kind of like exploring your file explorer but without a graphic interface so you have to type in the things you normally click. This movement will be done with the change directory or "cd" function. To go backwards a directory  you will use "cd .." to go back multiple directories you can use "cd ../../.." the "/.." will send you back an extra directory for each one you enter. You can use "ls" to see the files and folders in your current directory. If we wanted to enter the home folder we would type in "cd home". However if we know where we want to go we can enter the whole filepath as shown next"

2. We know we want to be in /var/www/html/ so we could enter "cd /var/www/html" and it would take us right there. But first we will go to our var by inputting "cd /var" file and add permissions to our user. 

3 We will use the chown command to allow our user permission to manipulate the www directory and the "-R" function will allow us to recursively manipulate anything included within. The Sudo command will grant us administrative privilege on this command to allow it to happen. "sudo chown -R username password"

4. We will then navigate into our file with "cd /www/html"

```sh
U@ubuntu~$ cd /var
U@ubuntu:/var$ sudo chown -R U www
[sudo] password for U: Pa$$
U@ubuntu:/var$ cd /www/html
U@ubuntu:/var/www/html
```

### Installing PHP and MYSQL, Both are necessary additions for wordpress to function. We will download these through the terminal onto our web server. 

#### Remember the sudo command may still be needed even if you have granted your user permission on this directory, any system changes will require a sudo command. So if it asks for permission. Try the same command with "sudo" infront of it. Also make sure to watch as files download. You may be prompted to hit the enter key or type a "y" to continue.

1. The first step is to perform an update with the "sudo apt-get update" function. It will then prompt you for you password and make sure your server is up to date. It is good practice to update frequently as you proceed.

2. You will now have to retrieve a repository from online. Although it is free to use, opensource tech the repository is hosted by Ondrej Sury. We can get this repository by using the "sudo add-apt-repository ppa:ondrej/php" the ppa:ondrej/php aspect is where this repository is being hosted and the add-apt-repository function collects it. Press enter when prompted

3. We will now update again with "sudo apt-get update"

4. Now that we have the repository on our server we can download it by entering "sudo apt-get install php7.2" follow allong with the installand type "y" then enter key when it prompts.

5. To double check our work we will enter the "php -v" command which will ideally respond "PHP 7.2..."

```sh
U@ubuntu:/var/www/html$ sudo apt-get update  
hit1:...
...Done  
U@ubuntu:/var/www/html$ sudo add-apt-repository ppa:ondrej/php  
Co-installable...
...press[ENTER] to continue
...OK  
U@ubuntu:/var/www/html$ sudo apt-get update  
Hit1...  
...Done  
U@ubuntu:/var/www/html$ sudo apt-get install php7.2  
Reading package lists...  
... Do you want to continue? [Y/n] y  
...Processing triggers  
U@ubuntu:/var/www/html$ php -var  
PHP 7.2...  
```

### Making sure Apache2 has the correct PHP configured.

1. First we will check and see if we have the older PHP configured to our apache2. We will do this by typing in "ls /etc/apache2/mods-enabled/php*" This is using the ls function to list the files in the /etc/apache2/mods-enabled/php directory that are currently enabled. If the result ends php7.2.conf and php7.2.load then you can ignore the next steps.

2. If the results of the last test were **not** "/etc/apache2/mods-enabled/php7.2.conf" and "/etc/apache2/mods-enabled/php7.2.load" we will need to proceed.

3. Disable the old php versions by using the command "sudo a2dismod php7.0" If the mods enabled file had a version other than 7.2 or 7.0 use that command to disable whichever configuration is running just change the number.

4. We will now enable the new PHP we downloaded by enterring "sudo a2enmod php7.2".

5. No we will restart apache2 with the "sudo service apache2 restart" command and then double check the enabled php with "ls /etc/apache2/mods-enabled/php*"

```sh
U@ubuntu:/var/www/html$ ls etc/apache2/mods-enabled/php*  
/etc/apache2/mods-enabled/php7.0.conf /etc/apache2/mods-enabled/php7.0.load  
U@ubuntu:/var/www/html$ sudo a2dismod php7.0  
U@ubuntu:/var/www/html$ sudo a2enmod php7.2  
U@ubuntu:/var/www/html$ sudo service apache2 restart  
U@ubuntu:/var/www/html$ ls etc/apache2/mods-enabled/php*  
/etc/apache2/mods-enabled/php7.2.conf /etc/apache2/mods-enabled/php7.2.load
```

### Next up we will be installing MYSQL 5.7

1. First thing we will update again with "sudo apt-get update"

2. Next we will install MYSQL onto our web server with "sudo apt-get install mysql-server". You will be prompted again to continue. Again you will enter "y" to continue.You will be asked during installation to create a root password. This is important so make sure it is secure but also something you can remember. For this example we will use Pa$$

3. For added security we will run the script mysql_secure_installation. You will follow through the prompts and hit Y then enter to make changes. These are all up to you and are additional security measures. You can definitely ignore the change password prompt, and I also allow root login remotely.

4. To make sure that MYSQL is running we will enter the command "systemctl status mysql.service" The output should say Active:active running If it does then congratulations, if not we can try to manually start it by entering "sudo systemctl start mysql".

```sh
U@ubuntu:/var/www/html$ sudo apt-get update  
[sudo] password for U: Pa$$  
Hit:1...
...Done  
U@ubuntu:/var/www/html$ sudo apt-get install mysql-server  
Reading package lists...
...Do you want to continue? [Y/n] y
enter password: Pa$$
... Processing triggers for ureadahead (0.100.0-19)  
U@ubuntu:/var/www/html$ mysql_secure_installation
Securing the MYSQL... 
Choose your settings with y/n...
All done!  
U@ubuntu:/var/www/html$ systemctl status mysql.service  
mysql.service - MySQL Community Server
Loaded: loaded ...
Active: active (running) ...
... Started MySQL Community Server.
```

### Additional downloads so that PHP works with MySQL
#### By now you are a pro at downloading packages so there will be a little less information on those touched on aspects.

1. First we will update again with "sudo apt-get update". Now we can install a way for apache to communicate better with php. Without this apache2 doesn't recognize PHP correctly and will only see it as a raw file. To allow apache2 to understand how to interact with PHP we will call "sudo apt-get install libApache2-mod-php7.2"

2. Next we will install a module that helps facilitate the interaction between our new PHP and MySQL downloads. This is called with "sudo apt-get install php7.2-mysql"

3. The final installation is a multibyte string encoder for php. It's used to express non-ASCII strings and is important when using wordpress. We will install it by calling "sudo apt-get install php7.2-mbstring"

```sh
U@ubuntu:/var/www/html$ sudo apt-get update
...
U@ubuntu:/var/www/html$sudo apt-get install libApache2-mod-php7.2
...
U@ubuntu:/var/www/html$ sudo apt-get install php7.2-mysql
...
U@ubuntu:/var/www/html$ sudo apt-get install php7.2-mbstring
...
U@ubuntu:/var/www/html$
```

### Setting up a database in MySQL
#### Ok great work so far, now would be a good time to take a quick break, grab a coffee or a top up on your wine then we'll move forward. Next we will be setting up a MySQL database to connect to our wordpress download. This would also be a good time to take a snapshot of your server. An easy place to restart from if some other issues come up.

1. The first step is to enter into MySQL through our terminal. We can do this by following the protocol of "MySQL -u Username -p". Remember that with MySQL the user is root not your Ubuntu username. It will then prompt you for a password which you will enter from when we first installed it.

2. Now that we are inside our MySQL tje first thing we will do is create a user. I will write down the syntax here and you can see practical input in the code area. "CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'password';" 

3. Next we will create our database that we plan on linking to our wordpress with "CREATE DATABASE databasename;

4. and finally we need to grant our user full capacity to manipulate the database that we also created. We can do that with the code "GRANT ALL PRIVILEGES ON databasename,* TO 'wordpressuser'@'localhost';"

5. Then you will enter "quit" to leave MySQL. Remember to check the code below MySQL looks different than your normal terminal, don't worry, it's supposed to.

```sh
U@ubuntu:/var/www/html$ MySQL -u root -p  
Enter password: Pa$$
mysql> CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'Pa$$';
Query OK, 0 rows affected (0.00 sec)  
mysql> CREATE DATABASE wordpressdatabase;
Query OK, 1 row affected (0.00sec)  
mysql> GRANT ALL PRIVILEGES ON wordpressdatabase.* TO 'wordpressuser'@'localhost';
Query OK, 0 rows affected (0.00 sec)  
mysql> quit
Bye
U@ubuntu:/var/www/html$
```

### Downloading and Unzipping Wordpress Folder
#### If you got the seem responses I did then we are on the right track, it's time to install wordpress to our web server.

1. The first thing we will do is call a curl command onto the wordpress site where components folder is. The internet is truly just made up of stacks of documents. A variety of different documents and filetypes create everything you see the curl command can allow you to grab these files if you know where they are being hosted. Luckily for us wordpress encourages this and they host it in it's entirity at "wordpress.org/latest.zip". The -O is just a naming function that names the file the end of the url.We will grab this file by inputting "curl -O  https://wordpress.org/latest.zip"

2. Our file is now in our directory. We can see it with the "ls" command. However it has been compresssed into a .zip file. Luckily, these can easily be unzipped if we download an unzipper to our server. We will do that by calling "sudo apt-get install unzip"

3. Now we will unzip the directory by inputting "unzip latest.zip" You will now see the wordpress folder sitting in your folder if you use "ls" again. Let's delete that by using the rm function "rm latest.zip".

4. Finally we will restart apache2 with the command "sudo service apache2 restart" Now if you navigate to the server's ip address in your browser, you should see the wordpress folder. If you click on it in your browser you will be able to enter the set up.

```sh
U@ubuntu:/var/www/html$ curl -O https://wordpress.org/latest.zip  
U@ubuntu:/var/www/html$ ls
latest.zip  
U@ubuntu:/var/www/html$ sudo apt-get install unzip
[sudo] password for U: Pa$$
Reading Package...
...Setting up unzip(6.0-20ubuntu1)  
U@ubuntu:/var/www/html$ unzip latest.zip
Archve: latest.zip...
...
inflating...
...inflating: wordpress/wp-comments-posts.php
U@ubuntu:/var/www/html$ ls
latest.zip wordpress  
U@ubuntu:/var/www/html$ rm latest.zip
U@ubuntu:/var/www/html$ sudo service apache2 restart
```

### Almost done, now we merge the MySQL with our wordpress.


1. At this point if you have put your server's IP address into the url bar of your web browser, then clicked on the wordpress folder you should see a page that says "Welcome to WordPress." and let's you know that it needs database info to move on. That's what we set up when we were in the MySQL. Click on the "Let's go!" Button.

2. We will fill out the form with the following information.
 * Database Name = wordpressdatabase
 * Username = wordpressuser
 * Password = Pa$$
 * Database = localhost
 * Table Prefix = wp_
 
 ![wordpress_database](https://user-images.githubusercontent.com/73308063/101961862-83be2100-3bbf-11eb-9c4d-8834fd9beb6f.png)
 
3. After we enter in this data and hit submit we will enter a new page that has a text box. This is a configuration file that has been generated for us given the information that was provided in the previous page. We will now copy this information and head back to our terminal.

![wordpress_config](https://user-images.githubusercontent.com/73308063/101962129-29719000-3bc0-11eb-9089-798d8699b1ad.png)


4. Back in our terminal we will navigate into our wordpress folder with "cd wordpress" and inside we will use nano editor to create a file called "wp-config.php". We will paste the information we previously copied into it by double right clicking when using windows command prompt terminal. The command to create the file is "sudo nano wp-config.php"

5. To save this file we will hit Ctrl+o then enter. To leave we will press Ctrl+x then enter. Now we will list the files with "ls" and see our new wp-config.php file is inside. We can also see a wp-config-sample.php file that can be removed with "rm wp-config-sample.php" if you choose.

6. Head back to your browser and click on the "run the installation" button. And there you have it. You have your wordpress fully put onto your webserver and ready to be set up.

```sh
U@ubuntu:/var/www/html$ cd wordpress
U@ubuntu:/var/www/html/wordpress$ sudo nano wp-config.php
---Paste your generated configuration here---
Ctrl+o to save
Ctrl+x to exit
U@ubuntu:/var/www/html/wordpress$ rm wp-config-sample.php
```



### Congratulations!!!


