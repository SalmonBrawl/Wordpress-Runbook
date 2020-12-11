# Wordpress RunBook

## Hello friends, here I will be showing you how to fully install wordpress onto your web server. Anything written in ** bold ** will be user entry, "..." symbolizes text that is not important to our current goals

### The first step is to SSH into your server.  
1. To SSH into your server you need to first find your server's ip address as well as collect the username and password of the server.

2. Next you will run the command "ssh username@ipAddress" if your SSH has been modified to a different port then you may include that with "-p portNumber" however standard SSH is through port 22.

3. It may prompt you for a password, you will then enter your user's password.

4. Confirm you are in your webserver by running "ifconfig" it should return that you have opened a shell in the webserver.

For examples sake, our IP adress will be 10.10.10.10, our username will be "U" and our password will be "Pa$$".

```sh
C:\Users\SalmonBrawl> ** ssh U@10.10.10.10 **
U@10.10.10.10's password: ** Pa$$ **
Welcome to _Ubuntu 10.04.6_ LTS... 
U@ubuntu:~$ ** ifconfig **
... inet addr:10.10.10.10...
```
