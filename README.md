# Local WordPress dev setup

Good luck! Hit me up if you need anything that I forgot.  
*-Sal*

**Notes:**

---
`this is a terminal command`  

```
this is config file text  
```  

---
* [Set up Apache, MySQL and PHP](https://coolestguidesontheplanet.com/get-apache-mysql-php-and-phpmyadmin-working-on-macos-sierra/)
  *  I prefer [Sequel Pro](https://www.sequelpro.com/) over installing PHPMyAdmin locally, so you can skip that step
* install a [WordPress](https://wordpress.org/) site in your `~/Sites` directory
  * make a virtual host for ~/Sites to sites.dev: `sudo nano /etc/apache2/extra/httpd-vhosts.conf`
 
```
<VirtualHost *:80>  
    ServerAdmin schlub@howtomeetladies.com,  
    DocumentRoot "/Users/{user}/Sites"  
    ServerName sites.dev  
    ServerAlias sites.dev www.sites.dev  
    ErrorLog "/private/var/log/apache2/sites.dev-error_log"  
    CustomLog "/private/var/log/apache2/sites.dev-access_log"   common
</VirtualHost>
```

  * `sudo nano /etc/hosts` -> add the following line: 

```
127.0.0.1           sites.dev www.sites.dev
```
  

*  at minimum, PHP version should be [PHP](http://php.net/manual/en/install.php) >= 5.6.4
  * `php --version` to check version
  * [Update PHP to 7.0 macOS Sierra](https://medium.com/zenchef-tech-and-product/how-to-upgrade-your-version-of-php-to-7-0-on-macos-sierra-e1bfdea55a63)
* install [Composer](https://getcomposer.org/download/)
* make sure your [Node.js](http://nodejs.org/) >= 6.9.x
* install [Yarn](https://yarnpkg.com/lang/en/docs/install/)
* change directories to your projects folder: `cd ~/Projects`
* clone repository to projects folder: `git clone https://github.com/salvatorious/aldrich-advisors.git`
