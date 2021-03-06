## WP PHP vX.x+ (Version Check w/ Dashboard Notice)

Stub for WordPress themes/plugins that require PHP vX.x+ (i.e. a minimum version that you define).

![](assets/screenshot.png)

---

### Example Usage in a Typical WordPress Theme/Plugin File

```php
<?php
/*
	Plugin Name: My Plugin
	Plugin URI: http://example.com/my-plugin
	Description: Example plugin.
	Author: Example Author.
	Version: 0.1-alpha
	Author URI: http://example.com
	Text Domain: my-plugin
*/
$GLOBALS['wp_php_rv'] = '5.3'; // Require PHP vX.x+ (you configure this).
if(require('wp-php-rv/src/includes/check.php')) // `true` if running PHP vX.x+.
	require dirname(__FILE__).'/my-plugin-code.php'; // It's OK to load your plugin.
else wp_php_rv_notice(); // Creates a nice PHP vX.x+ dashboard notice for the site owner.
```

---

### Alternate Approach

The `check.php` file will automatically return `true` upon using `include()` or `require()` in your scripts; i.e., iff the installation site is running PHP vX.x+ (as configured by `$GLOBALS['wp_php_rv']`). Otherwise it returns `false`. Therefore, the simplest way to run your check is to use `if(require('wp-php-rv/src/includes/check.php'))`. **However**, you could also choose to do it this way.

```php
<?php
$GLOBALS['wp_php_rv'] = '5.3'; // Require PHP vX.x+.
require 'wp-php-rv/src/includes/check.php'; // Include.

if(wp_php_rv()) // `true` if running PHP vX.x+.
	require dirname(__FILE__).'/my-plugin-code.php'; // It's OK to load your plugin.
else wp_php_rv_notice(); // Creates a nice PHP vX.x+ dashboard notice for the site owner.
```

---

### Dashboard Notice that Calls your Software by Name

```php
<?php
$GLOBALS['wp_php_rv'] = '5.3'; // Require PHP vX.x+.
if(require('wp-php-rv/src/includes/check.php')) // `true` if running PHP vX.x+.
	require dirname(__FILE__).'/my-plugin-code.php'; // It's OK to load your plugin.
else wp_php_rv_notice('My Plugin'); // Dashboard notice mentions your software specifically.
```

**Note:** If you omit the `$brand_name` argument, a default value is used instead. The default value is `ucwords('[calling file basedir]')`; e.g., if `/my-plugin/stub.php` calls `wp-php-rv/src/includes/check.php`, the default `$software_name` automatically becomes `My Plugin`. Nice!

---

### What if multiple themes/plugins use this?

This is fine! :-) The `wp-php-rv/src/includes/check.php` file uses `function_exists()` as a wrapper; which allows it to be included any number of times, and by any number of plugins; and also from any number of locations. **The only thing to remember**, is that you MUST be sure to define `$GLOBALS['wp_php_rv']` each time; i.e., each time you `include('wp-php-rv/src/includes/check.php')` or `require('wp-php-rv/src/includes/check.php')`.

The point here, is that `$GLOBALS['wp_php_rv']` defines a PHP version that is specific to your plugin requirements, so it should be defined explicitly by each plugin developer before they `include('wp-php-rv/src/includes/check.php')` or `require('wp-php-rv/src/includes/check.php')`.

---

### Can this just go at the top of my existing theme/plugin file?

**No, there are two important things to remember.**

1. Don't forget to bundle a copy of the `websharks/wp-php-rv` repo with your theme/plugin. All you really need is the `/src` directory.
2. Don't leave your existing code in the same file. Use this in a stub file that checks for PHP vX.x+ first (as seen in the examples above), BEFORE loading your code which depends on PHP vX.x+. Why? If you put a PHP vX.x+ check at the top of an existing PHP file, and that particular PHP file happens to contain code which is only valid in PHP v5.2 (for instance), it may still trigger a syntax error. For this reason, you should move your code into a separate file and create a stub file that checks for the existence of PHP vX.x+ first.

---

### Can I test for required PHP extensions too?

Yes, `$GLOBALS['wp_php_rv']` can be either a string with a required version, or an array with both a required version and a nested array of required PHP extensions. The easiest way to show how this works is by example (as seen below). Note that your array of required PHP extensions must be compatible with PHP's [`extension_loaded()`](http://php.net/manual/en/function.extension-loaded.php) function.

```php
<?php
$GLOBALS['wp_php_rv']['min'] = '5.3';
$GLOBALS['wp_php_rv']['extensions'] = array('curl', 'mbstring');

if(require('wp-php-rv/src/includes/check.php')) // `true` if running PHP vX.x+ w/ all required extensions.
	require dirname(__FILE__).'/my-plugin-code.php'; // It's OK to load your plugin.
else wp_php_rv_notice('My Plugin'); // Dashboard notice mentions your software specifically.
```

---

### What else can I test for with this system?

A compatible OS, a compatible PHP version, required bits, required PHP functions, required PHP extensions, and a compatible WP version.

```php
<?php
$GLOBALS['wp_php_rv']['os'] = 'nix'; // Requires a Unix-like OS.
// This is one of two operating system identifiers (always in lowercase): `nix` or `win`
// As far as WP PHP RV is concerned, their OS is either `nix` (Unix-like) or `win` (Windows).
// If you only want to support Unix-like systems, set this to: `nix`, making Windows incompatible.
// If you only want to support Windows systems, set this to: `win`, making others incompatible.

$GLOBALS['wp_php_rv']['min'] = '5.3'; // Minimum PHP version.
$GLOBALS['wp_php_rv']['max'] = '7.0.4'; // Max compatible PHP version, if applicable.

$GLOBALS['wp_php_rv']['bits'] = 64; // e.g., 32 or 64 bit architecture.
$GLOBALS['wp_php_rv']['functions']  = array('eval'); // Functions (or constructs).
$GLOBALS['wp_php_rv']['extensions'] = array('curl', 'mbstring'); // See previous FAQ.

$GLOBALS['wp_php_rv']['wp']['min'] = '4.2'; // Minimum WP version.
$GLOBALS['wp_php_rv']['wp']['max'] = '4.5.2'; // Max compatible WP version, if applicable.

if(require('wp-php-rv/src/includes/check.php')) // `true` if no issue.
	require dirname(__FILE__).'/my-plugin-code.php'; // It's OK to load your plugin.
else wp_php_rv_notice('My Plugin'); // Dashboard notice mentions your software specifically.
```

---

Copyright: © 2015 [WebSharks, Inc.](http://www.websharks-inc.com/bizdev/) (coded in the USA)

Released under the terms of the [GNU General Public License](http://www.gnu.org/licenses/gpl-3.0.html).
