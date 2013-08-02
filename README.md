WordPress on OpenShift
======================

This repository's purpose is to quickly get WordPress up and running on OpenShift. Under normal circumstances you only need to pull this repository and push it to your OpenShift app repository.


Installation
------------

Open an account at http://openshift.redhat.com/ if you don’t have one already and make sure you have the `rhc` gem installed.
Then one command is all you need to deploy a fresh WordPress powered website on OpenShift:

```
rhc app create wordpress php-5 mysql-5 --from-code=https://github.com/iclanzan/WordPress-on-OpenShift
```


Notes
-----

WordPress lives in a subdirectory, specifically `php/wordpress`, to make updating it easy. In order to update WordPress one needs to replace `php/wordpress` with the new version.

Because of the above consideration, the `wp-content` directory needs to sit outside of `php/wordpress` so it is set to `php/conent`.

The WordPress configuration file is `php/wp-config.php` and sits outside the WordPress root directory as well.

When first accessing your app a set of secret keys and salts are requested from https://api.wordpress.org/secret-key/1.1/salt/ and on each build they are added to the config file. If you need to have new keys generated for you just remove the `../data/secret-keys.php` file.

The WordPress Site URL and Home URL are dynamically set inside the config file based on the value of the `$_SERVER['HTTP_HOST']` variable.

When debugging, set `define('WP_DEBUG', false);` to `define('WP_DEBUG', true);`. Errors will be logged to `$OPENSHIFT_LOG_DIR/debug.log`.

The uploads directory is set to `$OPENSHIFT_DATA_DIR/uploads` because anything placed in the `$OPENSHIFT_DATA_DIR` directory is persistent. In order for WordPress to be able to use this directory though, a symbolic link to this directory is created at `php/content/uploads`.

The `php/.htaccess` file that WordPress creates is a symlink to `$OPENSHIFT_DATA_DIR/htaccess` to preserve it.
