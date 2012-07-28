WordPress on OpenShift
======================

This repository's purpose is to quickly get WordPress up and running on OpenShift. Under normal circumstances you only need to pull this repository and push it to your OpenShift app repository.


Installation
------------

Open an account at http://openshift.redhat.com/ if you don't have one already and create a new `php-5.3` application.
Add the MySQL cartridge to your new app.
Clone your app's git repository to your computer and `cd` into it.
Add this git repository as an upstream server and then `pull` and `push` like this:

	git remote add upstream -m master git://github.com/iclanzan/WordPress-on-OpenShift.git
	git pull -s recursive -X theirs upstream master
	git push

Done!


Notes
-----

WordPress lives in a subdirectory, specifically `php/wordpress`, to make updating it easy. In order to update WordPress one needs to replace `php/wordpress` with the new version.

Because of the above consideration, the `wp-content` directory needs to sit outside of `php/wordpress` so it is set to `php/conent`.

The `php/wp-config.php` is the WordPress configuration file.

When first accessing your app a set of secret keys and salts are requested from https://api.wordpress.org/secret-key/1.1/salt/ and on each build they are added to the config file. If you need to have new keys generated for you just remove the `../data/secret-keys.php` file.

The WordPress Site URL and Home URL are dynamically set inside the config file based on the value of the `$_SERVER['HTTP_HOST']` variable.

When debugging, set `define('WP_DEBUG', false);` to `define('WP_DEBUG', true);`. Errors will be logged to `$OPENSHIFT_LOG_DIR/debug.log`.

The uploads directory is set to `$OPENSHIFT_DATA_DIR/uploads` because anything placed in the `data` directory is persistent. In order for WordPress to be able to use this directory though, a symbolic link to this directory is created at `php/uploads`.

The `php/.htaccess` file that WordPress creates is a symlink to `$OPENSHIFT_DATA_DIR/htaccess` to preserve it.
