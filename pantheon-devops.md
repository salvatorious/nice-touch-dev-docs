# [Pantheon local development guide that this is based off of](https://pantheon.io/docs/local-development/https://pantheon.io/docs/local-development/)
  


## Install Terminus (Pantheon command-line client)
- Install Terminus via Homebrew: `brew install homebrew/php/terminus`  
- Generate a machine token (click this link when you're logged into Pantheon) [https://dashboard.pantheon.io/account#account/tokens](https://dashboard.pantheon.io/account#account/tokens)
- Paste in the command, it should be something like `terminus auth:login --machine-token=gibberishstuff`  
- After that initial login, you should be able to perform subsequent logins with: `terminus auth:login --email=email@example.com`
- List sites with Terminus: `terminus site:list` 

## Install WP-CLI
- `curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar`  
- `chmod +x wp-cli.phar`  
- `sudo mv wp-cli.phar /usr/local/bin/wp`    
- Then, run `wp --info` to verify that it works    
  

## Clone Pantheon site to Homestead shared folder
  
  
**Code & WordPress**  
Go to your site's Dashboard->Code tab, click on Git mode under Connection Mode, and copy the git clone command. Navigate to your Homestead shared folder (mine is ~/Code), and paste in the command (the git clone will include a folder, you don't need to create one).  
  
**Database**
- Create remote db backup:  `terminus backup:create <site>.<env> --element=db`  
- Get remote db backup (downloads to whatever dir you're in):  `terminus backup:get <site>.<env> --element=db | xargs curl -o database.sql.gz`  
- Import backup file to Homestead MySQL (database needs to be declared in your Homestead.yaml file):  `gunzip < database.sql.gz | mysql -uhomestead -psecret databasename --host=127.0.0.1 --port=33060`  
- Create a `~/Code/<site>/wp-config-local.php` file and gitignore it, so you can have your local db info in here (this should be one of the databases listed in your Homestead.yaml file).
    ```php
    <?php
    define( 'DB_NAME', 'databasename' );
    define( 'DB_USER', 'homestead' );
    define( 'DB_PASSWORD', 'secret' );
    define( 'DB_HOST', '127.0.0.1' );
    // define( 'DB_HOST', '127.0.0.1:33060' ); // uncomment this when you need to use wp-cli
    define( 'WPLANG', '' );
    define( 'WP_DEBUG', true );
    define( 'SCRIPT_DEBUG', true );
    ```
Now, you can migrate your URLs in the database with WP-CLI:  
- Navigate to WordPress root directory, then run `wp search-replace 'http://oldpantheonurl.com' 'http://yourlocalurl.test'`  
- Reverse the commented db host lines in `wp-config-local.php`  
  
  
**Files**
- Create remote file backup:  `terminus backup:create <site>.<env> --element=files` (this can take a while)  
- `cd ~/Code/<site>/wp-content/`  
- Get remote file backup and copy into current dir:  `terminus backup:get <site>.<env> --element=files | xargs curl -o files.tar.gz`  
- Unzip files backup:  `gunzip files.tar.gz`  
- Rename containing folder to "uploads"  
- Fix permissions: `sudo chown -R _www ~/Code/<site> ; sudo chmod -R g+w ~/Code/<site>`

**WARNING**  
Reprovisioning Homestead db with `homestead reload --provision` will wipe out the database and you'll have to redo the import and migrate steps.  