Configula
=========

Configula is a simple configuration library for PHP 5.3+.  

Features
--------
* Works with .php, .ini, .json, and .yaml configuration files

* Easily write a plugin to support other filetypes

* Simple usage:

    //Access configuration values
    $config = new Configula\Config('/path/to/config/files');
    $some_value = $config->get_value('some_key');

* Property-like access to your config settings:

    //Access configuration values
    $config = new Configula\Config('/path/to/config/files');
    $some_value = $config->some_key;

* Packagist/Composer and [PSR-0 Compliant](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md, "PSR-0 Standards Explanation")

* Unit-Tested!  100% coverage, not including certain YAML libraries.


Installation
------------

###Installation via source:

1. Download from [Github](http://github.com/caseyamcl/Configula, "Github Page for Configula")
2. Drop the _Configula_ folder into your codebase
3. Use a [PSR-0 Autoloader](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md, "PSR-0 Standards Explanation") to include it!

###Installation via Packagist:

Include the following in your _composer.json_ file:

    "require": {
      ...
      "caseyamcl/Configula": "dev-master"
    }

Then, run <code>php composer.phar install</code>

Basic Usage
-----------

1. Create a single folder in your application for storing configuration files.
2. Populate the folder with configuration files.  See _Config Folder Layout_ section below for more details.
3. Instantiate a configula instance, and send the path as the first parameter:

    $config = new Configula\Config('/path/to/app/config');

4. Configuration values become properties of the Configula object:

    $my_value = $config->my_config_setting;

5. Alternatively, use the <code>get_item()</code> method, which accepts an optional default value:

    $my_value = $config->get_item('my_config_setting', 'default_to_fall_back_on');

6. To get all config settings as an array, use the <code>get_item()</code> method, but do not send any parameters:

    $all_values = $config->get_item();

7. If you would like to preload the config object with default values, send those as the second parameter upon instantiation:

    //Values in the config files will override the default values
    $default_values = array('foo' => 'bar', 'baz' => 'biz');
    $config = new Configula\Config('/path/to/app/config', $default_values);

8. If you would like to use Configula with only default values, do not provide a path to the configuration directory:

    //The default values will be the only config options available
    $default_values = array('foo' => 'bar', 'baz' => 'biz');
    $config = new Configula\Config(NULL, $default_values);

Notes:

* The Config object, once instantiated, is immutable, meaning that it is read-only.  You can not alter the config values.  You can, however, create as many Configula objects as you would like. 
* If any configuration file contains invalid code (invalid PHP or malformed JSON, for example), the Configula class will not throw an error.  Instead, it will simply skip reading that file.


Config Folder Layout
--------------------

You can use any single folder to store configuration files.  You can also mix and match any supported configuration filetype.  Current supported filetypes are:

* __PHP__ - Configula will look for an array called <code>$config</code> in this file.
* __JSON__
* __YAML__ - YAML parsing support is built-in.  You do not need an external library of PECL package for this.
* __INI__

### Local Configuration Files

In some cases, you may want to have local configuration files that override the default configuration files.  To override any configuration file, create another configuration file, and append <code>.local.EXT</code> to the end.

For example, a configuration file named <code>database.yaml</code> is overridden by <code>database.local.yaml</code>, if the latter file exists.

This is very useful if you want certain settings included in version control, and certain settings not versioned (just add <code>/path/to/config/*.local.EXT</code> to your <code>.gitignore</code> or equivalent VCS file)


Writing Your Own Configuration File Type Support
------------------------------------------------

In addition to the built-in filetype drivers, you can add your own driver for reading configuration files.  An example would look like

    namespace Configula\Drivers;

    class MyDriver implements Configula\DriverInterface {

      /**
       * Your read() method should accept a filepath string
       * and return an array.  Return an empty array if the
       * file is unreadable or unparsable for any reason.
       */
      public function read($filepath) {

        $contents = file_get_contents($filepath);
        return $this->parse ?: array();
      }

      /**
       * Example parse method
       */
      private function parse($contents) {

        /* ... code here .... */
        return $result ?: FALSE;
      }

    }

Refer to an existing Unit test for an example of how to test your driver class.

Todo
----

* Implement ArrayAccess and ArrayIterator interface
* Add better documentation
* Implement Exceptions for invalid configuration files