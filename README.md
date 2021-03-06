# About

<p align="center">
    <img title="Laravel Zero" height="100" src="https://raw.githubusercontent.com/laravel-zero/docs/master/images/logo/laravel-zero-readme.png" />
</p>

<p align="center">
  <a href="https://travis-ci.org/laravel-zero/framework"><img src="https://img.shields.io/travis/laravel-zero/framework/stable.svg" alt="Build Status"></img></a>
  <a href="https://scrutinizer-ci.com/g/laravel-zero/framework"><img src="https://img.shields.io/scrutinizer/g/laravel-zero/framework.svg" alt="Quality Score"></img></a>
  <a href="https://packagist.org/packages/laravel-zero/framework"><img src="https://poser.pugx.org/laravel-zero/framework/d/total.svg" alt="Total Downloads"></a>
  <a href="https://packagist.org/packages/laravel-zero/framework"><img src="https://poser.pugx.org/laravel-zero/framework/v/stable.svg" alt="Latest Stable Version"></a>
  <a href="https://packagist.org/packages/laravel-zero/framework"><img src="https://poser.pugx.org/laravel-zero/framework/license.svg" alt="License"></a>
</p>

<h4> <center>This is a <bold>community project</bold> and not an official Laravel one </center></h4>

Laravel Zero was created by, and is maintained by [Nuno Maduro](https://github.com/nunomaduro), and is a micro-framework that provides an elegant starting point for your console application. It is an **unofficial** and customized version of Laravel optimized for building command-line applications.

- Built on top of the [Laravel](https://laravel.com) components.
- Optional installation of Laravel [Eloquent](#database), Laravel [Logging](#log) and many others.
- Supports interactive [menus](#interactive-menus) and [desktop notifications](#desktop-notifications) on Linux, Windows & MacOS.
- Ships with a [Scheduler](#scheduler) and a [Standalone Compiler](#build-a-standalone-application).
- Integration with [Collision](https://github.com/nunomaduro/collision) - Beautiful error reporting

# Requirements & Installation

> **Requires [PHP 7.1.3+](https://php.net/releases/)**

Create a new Laravel Zero project using [Composer](https://getcomposer.org):

```bash
composer create-project --prefer-dist laravel-zero/laravel-zero <your-app-name>
```

To run your application, just execute in your application root folder:

```bash
php <your-app-name>
```

You can rename your application anytime by running the following command in your app directory:

```bash
php <your-app-name> app:rename <new-name>
```

# Usage

## App\Commands

Laravel Zero provides you with a `app\Commands\InspiringCommand.php` command as an example. Create a new command using:

```bash
php <your-app-name> make:command <NewCommand>
```

Concerning the Command file content, you may want to review the documentation of the Artisan Console component:

- The [Defining Input Expectations](https://laravel.com/docs/5.6/artisan#defining-input-expectations) section allows you to understand
 how to gather input from the user through arguments or options. As example:

```php
 protected $signature = 'user:create
                        {name : The name of the user} // required
                        {--age= : The age of the user}'; // optional.
```

- The [Command I/O](https://laravel.com/docs/5.6/artisan#command-io) allows you to understand how to capture those input expectations and
interact the with using commands like `line`, `info`, `comment`, `question` and `error` methods.

<a href="desktop-notifications"></a>
### Desktop notifications

<p align="center">
    <img src="https://raw.githubusercontent.com/nunomaduro/laravel-desktop-notifier/stable/docs/icon.png" width="50%">
</p>


```php
$this->notify("Hello Web Artisan", "Love beautiful..", "icon.png");
```

### Tasks

<p align="center">
    <img src="https://raw.githubusercontent.com/nunomaduro/laravel-console-task/master/docs/example.png" width="50%">
</p>

```php
$this->task("Installing Laravel", function () {
    return true;
});

$this->task("Doing something else", function () {
    return false;
});

```

### Interactive Menus

<p align="center">
    <img src="https://raw.githubusercontent.com/nunomaduro/laravel-console-menu/master/docs/example.png" width="50%">
</p>

```php
$option = $this->menu('Pizza menu', [
    'Freshly baked muffins',
    'Freshly baked croissants',
    'Turnovers, crumb cake, cinnamon buns, scones',
])->open();

$this->info("You have chosen the option number #$option");
```

Here’s how you can change the appearance of the menu with a fluent API:

```php
$this->menu($title, $options)
    ->setForegroundColour('green')
    ->setBackgroundColour('black')
    ->setWidth(200)
    ->setPadding(10)
    ->setMargin(5)
    ->setExitButtonText("Abort")
    // remove exit button with 
    // ->disableDefaultItems()
    ->setUnselectedMarker('❅')
    ->setSelectedMarker('✏')
    ->setTitleSeparator('*-')
    ->addLineBreak('<3', 2)
    ->addStaticItem('AREA 2')
    ->open();
```

### List of commands

The default command of your application contains a list of commands. That list of commands
can be configured using `config/commands.php`:

| Property  | Description
| ------------- | -------------
| default  | The default application command when no command name is provided.
| paths  | The "paths" that should be loaded by the console's kernel.
| add  | Here you may specify which commands classes you wish to include.
| hidden  | Adds the provided commands, but make them hidden.
| remove  | Removes the list of commands provided.

## App\ServiceProviders

Laravel Zero recommends the usage of [Laravel Service Providers](https://laravel.com/docs/5.6/providers) for defining concrete
implementations. Define them in `app\Providers\AppServiceProvider.php` or create new service providers.
The `config/app.php` *providers* array contains the registered service providers.
Below there is an example of a concrete implementation bound to a contract/interface.

```php
    public function register()
    {
        $this->app->singleton(Contract::class, function ($app) {
            return new Concrete(config('database'));
        });
    }

    app(Contract::class) // Returns a Concrete implementation.
```

## Config

The `config\app.php` file contains your application configuration, there you can create a new configuration `foo => true` and access to the
that same configuration using `config('app.foo')`.

All files within the `config` folder are automatically registered as configuration files.
You can also create specific configuration files, e.g: `app\bar.php` and access it with `config('bar')`.

Finally, before share your application with the world you should always set the application context to production by modifying `config/app.php`:

```php
    'production' => true,
```

## Tests

The `tests` folder contains your `phpunit` tests. By default, the Laravel Zero ships with a *Integration* suite that can be used like
the example below:

```php
use Tests\TestCase;
use Illuminate\Support\Facades\Artisan;

class InspiringCommandTest extends TestCase
{
    /**
     * A basic test example.
     */
    public function testInspiringCommand(): void
    {
        Artisan::call('inspiring');

        $this->assertContains('Leonardo da Vinci', Artisan::output());
    }
}
```

Running your application *tests*:

```bash
./vendor/bin/phpunit
```

<a href="database"></a>
## Database

Laravel Zero allows you to install a **Database** component out of the box to push your console app to the next level.
As you might have already guessed it, it is Laravel's [Eloquent](https://laravel.com/docs/5.6/eloquent) component that works like a breeze in the Laravel Zero environment too.

```bash
php <your-app-name> app:install database
```

Usage:

```php
use Illuminate\Support\Facades\DB;

DB::table('users')->insert(
    ['email' => 'enunomaduro@gmail.com']
);

$users = DB::table('users')->get();
```

Laravel [Database Migrations](https://laravel.com/docs/5.6/migrations) and [Database Seeding](https://laravel.com/docs/5.6/seeding) features are also included.

<a href="log"></a>
## Log

Laravel Zero allows you to install a **Log** component.

```bash
php <your-app-name> app:install log
```

Usage:

```php
use Illuminate\Support\Facades\Log;

Log::emergency($message);
Log::alert($message);
Log::critical($message);
Log::error($message);
Log::warning($message);
Log::notice($message);
Log::info($message);
Log::debug($message);
```

## Filesystem

If you want to move files in your system, or to multiple providers like AwsS3 and Dropbox, Laravel Zero ships with the [Filesystem](https://laravel.com/docs/5.6/filesystem) component by default.

Note: The root directory is `your-app-name/storage/app`.

```php
use Illuminate\Support\Facades\Storage;

Storage::put("reminders.txt", "Task 1");

```

<a href="scheduler"></a>
## Scheduler

Laravel Zero ships with the [Task Scheduling](https://laravel.com/docs/5.6/scheduling) system of Laravel. To use it, you may need to add the following Cron entry to your server:

```
* * * * * php /path-to-your-project/your-app-name schedule:run >> /dev/null 2>&1
```

You may define all of your scheduled tasks in the `schedule` method of the command:
```php
    public function schedule(Schedule $schedule): void
    {
        $schedule->command(static::class)->everyMinute();
    }
```

<a href="dotenv"></a>
## Environment Configuration

You may want to install the [DotEnv PHP](https://github.com/vlucas/phpdotenv) component.
It is often helpful to have different configuration values based on the environment the application is running in.

```bash
php <your-app-name> app:install dotenv
```

The installation will create an empty `.env.example` on your project root. You should rename it manually to `.env`.

Usage:
Assuming that your `.env` contains:
```
SECRET_KEY=234567
```

You can access those variables using the `env()` helper:
```php
echo env('SECRET_KEY') // outputs 234567
```

<a href="build-a-standalone-application"></a>
## Building a standalone application

Your Laravel Zero project, by default, allows you to build a standalone PHAR archive to ease the deployment or the distribution of your project.

```bash
php your-app-name app:build <your-build-name>
```

The build will provide a single phar archive, ready to use, containing all the code of your project and its dependencies. You will then be able to execute it directly:

```bash
./builds/<your-build-name>
```

or on Windows:

```bash
C:\application\path> php builds\<your-build-name>
```

## Collision - CLI Error Reporting

Love [Whoops](http://filp.github.io/whoops/) on Laravel? Get ready for Collision!

<p align="center">
    <img src="https://raw.githubusercontent.com/nunomaduro/collision/stable/docs/example.png" width="50%">
</p>

Get more details: [https://github.com/nunomaduro/collision](https://github.com/nunomaduro/collision).

## Tinker

[Laravel Tinker](https://github.com/laravel/tinker) is a powerful REPL for Laravel. [Jorge González](https://github.com/scrubmx) made it available for Laravel Zero.

Get more details: [https://github.com/intonate/tinker-zero](https://github.com/intonate/tinker-zero).

# Support & Community

Thank you for considering to contribute to Laravel Zero. All the contribution guidelines are mentioned [here](https://github.com/laravel-zero/laravel-zero/blob/stable/CONTRIBUTING.md).

You can have a look at the [CHANGELOG](https://github.com/laravel-zero/laravel-zero/blob/stable/CHANGELOG.md) for constant updates & detailed information about the changes. You can also follow the twitter account for latest announcements : [@enunomaduro](https://twitter.com/enunomaduro)

# License

Laravel Zero is an open-source software licensed under the [MIT license](https://github.com/laravel-zero/laravel-zero/blob/stable/LICENSE.md).
