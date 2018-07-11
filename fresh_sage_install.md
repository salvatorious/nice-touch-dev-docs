Assuming you have a fresh local Wordpress install...

# Requirements
* Git
* PHP >= 7.0
* Composer
* Node.js >=6.9.x
* Yarn
* WP-CLI

## Installing Sage without Composer
* Clone or Download the Zip

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

## Adding Test Data (Optional)
* `curl -OL https://raw.githubusercontent.com/poststatus/wptest/master/wptest.xml`
* `wp plugin install wordpress-importer --activate`
* `wp import wptest.xml --authors=create`
