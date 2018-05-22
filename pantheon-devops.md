# [Pantheon local development guide that this is based off of](https://pantheon.io/docs/local-development/https://pantheon.io/docs/local-development/)
  


## Install Terminus (Pantheon command-line client)
- Install Terminus: `mkdir ~/terminus && cd ~/terminus && curl -O https://raw.githubusercontent.com/pantheon-systems/terminus-installer/master/builds/installer.phar && php installer.phar install`  
- Generate a machine token (click this link when you're logged into Pantheon) [https://dashboard.pantheon.io/account#account/tokens](https://dashboard.pantheon.io/account#account/tokens)
- Paste in the command, it should be something like `terminus auth:login --machine-token=gibberishstuff`  
- After that initial login, you should be able to perform subsequent logins with: `terminus auth:login --email=email@example.com`
- List sites with Terminus: `terminus site:list` 
  

## Install WP-CLI
- `curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar`  
- `chmod +x wp-cli.phar`  
- `sudo mv wp-cli.phar /usr/local/bin/wp`    
- Then, run `wp --info` to verify that it works    
- Now, you can do a full find and replace from any of your WordPress root directories with: `wp search-replace 'http://oldurl.com' 'http://yourlocalurl.test'`   
  

## Clone Pantheon site to local folder
**Code & WordPress**  
Go to your site's Dashboard->Code tab, click on Git mode under Connection Mode, and copy the git clone command. Navigate to your local projects folder (mine is ~/Sites), and paste in the command (the git clone will include a folder, you don't need to create one).  
  
**Database**
- Copy this script to a file (`import-dev-db.sh` is fine), then run a `chmod +x` command on it.  
- Then, call it with `./import-dev-db.sh`    
```
#!/bin/bash

#Authenticate Terminus
terminus auth:login

#Provide the target site name (e.g. your-awesome-site)
echo 'Enter Pantheon site slug (e.g. your-awesome-site), then press [ENTER] to import the Dev database to your local mysql:';
read SITE;

#Create remote backup
terminus backup:create $SITE.dev --element=db

#Get remote db backup (downloads to whatever dir you're in) 
terminus backup:get $SITE.dev --element=db | xargs curl -o database-$SITE.sql.gz

echo 'MySQL user name:';
read USER;

echo 'MySQL password:';
read -s PASSWORD;

echo 'Name of your local database:';
read DATABASE;

#Unzip sql dump and import to mysql
gunzip < database-$SITE.sql.gz | mysql -u$USER -p$PASSWORD $DATABASE

#Clean up the db dump 
rm database-$SITE.sql.gz

echo 'Database imported!';

cd ~/Sites/$SITE;

wp search-replace http://dev-$SITE.pantheonsite.io http://sites.test/$SITE;

echo 'URLs migrated! All done!';

```

- Create a `~/Sites/<site>/wp-config-local.php` file and gitignore it, so you can have your local db info in here (this should be one of the databases listed in your Homestead.yaml file).
    ```php
    <?php
    define( 'DB_NAME', 'databasename' );
    define( 'DB_USER', 'dbuser' ); 
    define( 'DB_PASSWORD', 'passyboi' );
    define( 'DB_HOST', '127.0.0.1' );
    define( 'WPLANG', '' );
    define( 'WP_DEBUG', true );
    define( 'SCRIPT_DEBUG', true );
    ```
Now, you can migrate your URLs in the database with WP-CLI:  
- Navigate to WordPress root directory, then run `wp search-replace 'http://oldpantheonurl.com' 'http://yourlocalurl.test'`  
- Reverse the commented db host lines in `wp-config-local.php`  
  

**Files**
- Realistically, probably easiest to just download the file export and unzip into your wp-content/uploads folder...  
- Create remote file backup:  `terminus backup:create <site>.<env> --element=files` (this can take a while)  
- `cd ~/Code/<site>/wp-content/`  
- Get remote file backup and copy into current dir:  `terminus backup:get <site>.<env> --element=files | xargs curl -o files.tar.gz`  
- Unzip files backup:  `gunzip files.tar.gz`  
- Rename containing folder to "uploads"  
- Fix permissions: `sudo chown -R _www ~/Sites/<site> ; sudo chmod -R g+w ~/Sites/<site>`  

**OR** you can put this script in your home directory and run it with `~/.fix-permissions.sh`:  
```
#!/bin/bash

echo "Enter site slug to fix (script assumes that site is in ~/Sites)";
read SITENAME;

sudo chown -R _www ~/Sites/$SITENAME ; sudo chmod -R g+w ~/Sites/$SITENAME
```