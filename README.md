# Nice Touch Developer Docs
  
Good luck! Hit me up if you need anything that I forgot.  
*-Sal*

**Notes:**
`this is a terminal command`  

```
this is config file text  
```  

Local development process consists of the following:  
**Apache** is the webserver.  
- important config files are in `/etc/apache2/`.  
- after modifying config files, restart Apache with `sudo service apache2 restart`.  
**MySQL** is the database.  
**PHP** is the programming language that WordPress runs on.  
- Configuration for PHP is accomplished by modifying the php.ini file. You can find the location of this file by making a phpinfo.php file in your webroot, with the content `<?php phpinfo(); ?>`. You can then go to sites.test/phpinfo.php to see your full php profile.    



---
## Set up local WordPress dev environment 

* [Set up Apache, MySQL and PHP](https://coolestguidesontheplanet.com/install-apache-mysql-php-on-macos-mojave-10-14/)
  *  I prefer [Sequel Pro](https://www.sequelpro.com/) over installing PHPMyAdmin locally, so you can skip that step
* install a [WordPress](https://wordpress.org/) site in your `~/Sites` directory
  * make a virtual host for ~/Sites to sites.test: `sudo nano /etc/apache2/extra/httpd-vhosts.conf`

```
<VirtualHost *:80>  
    ServerAdmin schlub@howtomeetladies.com,  
    DocumentRoot "/Users/{user}/Sites"  
    ServerName sites.test  
    ServerAlias sites.test www.sites.test  
    ErrorLog "/private/var/log/apache2/sites.test-error_log"  
    CustomLog "/private/var/log/apache2/sites.test-access_log"   common
</VirtualHost>
```

  * `sudo nano /etc/hosts` -> add the following line: 

```
127.0.0.1           sites.test www.sites.test
```
  
*  at minimum, PHP version should be [PHP](http://php.net/manual/en/install.php) >= 5.6.4
  * `php --version` to check version
  * [Update PHP to 7.0 macOS Sierra](https://medium.com/zenchef-tech-and-product/how-to-upgrade-your-version-of-php-to-7-0-on-macos-sierra-e1bfdea55a63)
* install [Composer](https://getcomposer.org/download/)
* make sure your [Node.js](http://nodejs.org/) >= 6.9.x
* install [Yarn](https://yarnpkg.com/lang/en/docs/install/)
* change directories to your projects folder: `cd ~/Projects`
* clone theme repository to projects folder: `git clone https://github.com/salvatorious/aldrich-advisors.git`

## Copy Pantheon site to local environment  

* Copy remote plugins folder (`/code/wp-content/plugins`) to `~/Sites/localwhatever/wp-content/plugins`  
* Export the database and files via the Pantheon dashboard  ![Export Pantheon database](http://nicetouch.co/wp-content/uploads/2017/12/Screenshot-2017-12-17-18.09.50.png)  
* Copy the files into your local install at `~/Sites/localwhatever/wp-content/uploads`  
* You MAY need to fix local file permissions at this point. I use `sudo chown -R _www ~/Sites/aldrich ; sudo chmod -R g+w ~/Sites/aldrich`, but Alec's command to do this might be different because of his borky permission situation.  
* Unzip db dump `gunzip ~/Downloads/dump-file-name.tar.gz`  
* Import db to local database you've hopefully created `mysql -u root -p databasenamegoeshere < ~/Downloads/dump-file-name.sql`  
* Migrate local database urls  
```sql
UPDATE wp_options SET option_value = replace(option_value, 'http://dev-aldrich-advisors.pantheonsite.io', 'http://sites.test/aldrich') WHERE option_name = 'home' OR option_name = 'siteurl';
UPDATE wp_posts SET guid = replace(guid, 'http://dev-aldrich-advisors.pantheonsite.io','http://sites.test/aldrich');
UPDATE wp_posts SET post_content = replace(post_content, 'http://dev-aldrich-advisors.pantheonsite.io', 'http://sites.test/aldrich');
UPDATE wp_postmeta SET meta_value = replace(meta_value,'http://dev-aldrich-advisors.pantheonsite.io','http://sites.test/aldrich');
```
* Symlink a theme folder to point at the theme repo folder (which is probably somewhere in ~/Projects)  
* Try logging in at /wp-admin, if that works, then you're good! Don't forget to save your permalink settings.  
  
  
## Theme setup

Edit `app/setup.php` to enable or disable theme features, setup navigation menus, post thumbnail sizes, and sidebars.

## Theme development

* Run `yarn` from the theme directory to install dependencies
* Update `resources/assets/config.json` settings:
  * `devUrl` should reflect your local development hostname
  * `publicPath` should reflect your WordPress folder structure (`/wp-content/themes/sage` for non-[Bedrock](https://roots.io/bedrock/) installs)

### Build commands

* `yarn run start` — Compile assets when file changes are made, start Browsersync session
* `yarn run build` — Compile and optimize the files in your assets directory
* `yarn run build:production` — Compile assets for production  


### Fix for broken apache after MacOS update

- `sudo nano /etc/apache2/extra/httpd-vhosts.conf`
```
<VirtualHost *:80>
    ServerAdmin salvatore@nicetouch.co
    DocumentRoot "/Users/salvatore/Sites"
    ServerName sites.test
    ServerAlias sites.test www.sites.test
    ErrorLog "/private/var/log/apache2/sites.test-error_log"
    CustomLog "/private/var/log/apache2/sites.test-access_log" common
</VirtualHost>
```

- `sudo nano /etc/apache2/httpd.conf`  
- Uncomment all these lines (remove # sign from front of line)  

`#LoadModule authz_core_module libexec/apache2/mod_authz_core.so`  
`#LoadModule authz_host_module libexec/apache2/mod_authz_host.so`  
`#LoadModule userdir_module libexec/apache2/mod_userdir.so`  
`#LoadModule include_module libexec/apache2/mod_include.so`  
`#LoadModule rewrite_module libexec/apache2/mod_rewrite.so`  
`#LoadModule vhost_alias_module libexec/apache2/mod_vhost_alias.so`  

`#Include /private/etc/apache2/extra/httpd-vhosts.conf`

- Save file, then run `sudo apachectl restart`

### Switching from .dev to .test locally
- In {theme}/resources/assets/config.js, edit the `devUrl`  
- edit `/etc/apache2/extra/httpd-vhosts.conf`  
- edit `sudo nano /etc/hosts`  
- `sudo apachectl restart`  
- execute this SQL in the appropriate database:  
```sql
UPDATE wp_options SET option_value = replace(option_value, 'http://sites.dev/aldrich', 'http://sites.test/aldrich') WHERE option_name = 'home' OR option_name = 'siteurl';
UPDATE wp_posts SET guid = replace(guid, 'http://sites.dev/aldrich','http://sites.test/aldrich');
UPDATE wp_posts SET post_content = replace(post_content, 'http://sites.dev/aldrich', 'http://sites.test/aldrich');
UPDATE wp_postmeta SET meta_value = replace(meta_value,'http://sites.dev/aldrich','http://sites.test/aldrich');
```