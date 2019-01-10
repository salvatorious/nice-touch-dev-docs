# Install Wordpress

From your sites directory,
* run `curl -O https://wordpress.org/latest.tar.gz`
* then `tar -zxvf latest.tar.gz`

# Requirements
* Git
* PHP >= 7.0
* Composer `composer self-update`
* Node.js >=8.0.0 `nvm install node`
* Yarn `curl --compressed -o- -L https://yarnpkg.com/install.sh | bash`
* WP-CLI

## Installing Sage without Composer
* Clone or Download the Zip `git clone git@github.com:roots/sage.git`

From the theme directory, 
* run `composer install`
* then `yarn`

* Update the **config.json** with your local **devURL**

* Update the **webpack.config.json** to emit linting errors

```
    new StyleLintPlugin({
      failOnError: !config.enabled.watcher,
      emitErrors: true,
      quiet: false,
      syntax: 'scss',
    }),
```

* run `yarn build`
