#(https://pantheon.io/docs/local-development/)[https://pantheon.io/docs/local-development/]  
---

## Install Terminus (Pantheon command-line client)
- Install Terminus: `brew install homebrew/php/terminus`  
- Generate a machine token (click this link when you're logged into Pantheon) (https://dashboard.pantheon.io/account#account/tokens)[https://dashboard.pantheon.io/account#account/tokens]

## Clone Pantheon site to Homestead shared folder
- Create remote db backup: `terminus backup:create <site>.<env> --element=db`  
- Get remote db backup: `terminus backup:get <site>.<env> --element=db`  
- Import backup file to local MySQL: `gunzip < database.sql.gz | mysql -uUSER -pPASSWORD DATABASENAME`  
