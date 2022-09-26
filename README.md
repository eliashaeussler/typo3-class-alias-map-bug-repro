# TYPO3 class alias map bug

## Description

Whenever a [`Migrations/Code/ClassAliasMap.php`](packages/test_extension/Migrations/Code/ClassAliasMap.php)
file exists in a TYPO3 extension, running tests with the bootstrap file from TYPO3's testing framework fails
with the following message:

```
Error in bootstrap script: RuntimeException:
Cannot set an alias map as the alias loader is not registered!
#0 /var/www/html/public/typo3/sysext/core/Classes/Core/ClassLoadingInformation.php(142): TYPO3\ClassAliasLoader\ClassAliasMap::addAliasMap(Array)
#1 /var/www/html/vendor/typo3/testing-framework/Classes/Core/Testbase.php(648): TYPO3\CMS\Core\Core\ClassLoadingInformation::registerClassLoadingInformation()
#2 /var/www/html/vendor/typo3/testing-framework/Resources/Core/Build/UnitTestsBootstrap.php(81): TYPO3\TestingFramework\Core\Testbase->dumpClassLoadingInformation()
#3 /var/www/html/vendor/typo3/testing-framework/Resources/Core/Build/UnitTestsBootstrap.php(84): PHPUnit\Util\FileLoader::{closure}()
#4 /var/www/html/vendor/phpunit/phpunit/src/Util/FileLoader.php(66): include_once('/var/www/html/v...')
#5 /var/www/html/vendor/phpunit/phpunit/src/Util/FileLoader.php(49): PHPUnit\Util\FileLoader::load('/var/www/html/v...')
#6 /var/www/html/vendor/phpunit/phpunit/src/TextUI/Command.php(565): PHPUnit\Util\FileLoader::checkAndLoad('./vendor/typo3/...')
#7 /var/www/html/vendor/phpunit/phpunit/src/TextUI/Command.php(345): PHPUnit\TextUI\Command->handleBootstrap('./vendor/typo3/...')
#8 /var/www/html/vendor/phpunit/phpunit/src/TextUI/Command.php(112): PHPUnit\TextUI\Command->handleArguments(Array)
#9 /var/www/html/vendor/phpunit/phpunit/src/TextUI/Command.php(97): PHPUnit\TextUI\Command->run(Array, true)
#10 /var/www/html/vendor/phpunit/phpunit/phpunit(98): PHPUnit\TextUI\Command::main()
#11 /var/www/html/vendor/bin/phpunit(123): include('/var/www/html/v...')
#12 {main}
```

## Research

This problem seems to occur only under the following circumstances:

- File `Migrations/Code/ClassAliasMap.php` exists in a TYPO3 extension
- File `ext_emconf.php` exists in a TYPO3 extension

## Steps to reproduce

A test extension [`EXT:test_extension`](packages/test_extension) exists that contains said migration
file. The extension provides a [`composer.json`](packages/test_extension/composer.json) and an
[`ext_emconf.php`](packages/test_extension/ext_emconf.php.tmpl) template file.

Run the following steps to reproduce the failure:

```bash
# Start the project
ddev start
ddev composer install

# Run tests without an ext_emconf.php being present (=> Should be successful)
ddev composer test

# Run tests with an ext_emconf.php being preset (=> Should fail)
ddev composer test:with-ext-emconf
```
