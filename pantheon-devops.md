# [Pantheon local development guide that this is based off of](https://pantheon.io/docs/local-development/https://pantheon.io/docs/local-development/)
  


## Install Terminus (Pantheon command-line client)
- Install Terminus via Homebrew: `brew install homebrew/php/terminus`  
- Generate a machine token (click this link when you're logged into Pantheon) [https://dashboard.pantheon.io/account#account/tokens](https://dashboard.pantheon.io/account#account/tokens)
- Paste in the command, it should be something like `terminus auth:login --machine-token=gibberishstuff`  
- After that initial login, you should be able to perform subsequent logins with: `terminus auth:login --email=email@example.com`
- List sites with Terminus: `terminus site:list` 

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
    define( 'WPLANG', '' );
    define( 'WP_DEBUG', true );
    define( 'SCRIPT_DEBUG', true );
    ```
- Migrate your URLs in the database:
```SQL
UPDATE wp_options SET option_value = replace(option_value, 'http://dev-aldrich-advisors.pantheonsite.io', 'http://aldrich-advisors.test') WHERE option_name = 'home' OR option_name = 'siteurl';
UPDATE wp_posts SET guid = replace(guid, 'http://dev-aldrich-advisors.pantheonsite.io','http://aldrich-advisors.test');
UPDATE wp_posts SET post_content = replace(post_content, 'http://dev-aldrich-advisors.pantheonsite.io', 'http://aldrich-advisors.test');
UPDATE wp_postmeta SET meta_value = replace(meta_value,'http://dev-aldrich-advisors.pantheonsite.io','http://aldrich-advisors.test');
``` 
  
**Files**
- Create remote file backup:  `terminus backup:create <site>.<env> --element=files` (this can take a while)  
- `cd ~/Code/<site>/wp-content/`  
- Get remote file backup and copy into current dir:  `terminus backup:get <site>.<env> --element=files | xargs curl -o files.tar.gz`  
- Unzip files backup:  `gunzip files.tar.gz`  
- Rename containing folder to "uploads"  
- Fix permissions: `sudo chown -R _www ~/Code/<site> ; sudo chmod -R g+w ~/Code/<site>`

**WARNING**  
Reprovisioning Homestead db with `homestead reload --provision` will wipe out the database and you'll have to redo the import and migrate steps.  