# MvcCore Roadmap

- Documentation

- Controller:
  - For composing controllers using traits, it would be good to create methods in the controller:
    - `AddInitHandler()`,
    - `AddPreDispatchHandler()`,
    - `AddActionHandler()`,
    - `AddTerminateHandler()`.

- Automatic generation of type classes for display for hinting in the IDE.

- Router:
  - In addition to the canonical redirect feature, it would be good to implement some developer switch (usually turned off) that would check when creating URLs using `GetUrl()` whether the target controller/action/route exists and, if necessary, log somewhere or throw an exception.

- Running via PHP server
  - `php -S 127.0.0.1:8000 index.php`
  - PHPSAPI will be `cli-server` - and it is not certain whether all necessary `$_SERVER` values will be known.

- Refactoring interfaces
  - Some interfaces of certain extensions are nested non-standardly.
  - Interfaces are too bulky; they need to be split into smaller units.

- PHP static analysis:
  - Process using PHPStan even the most commonly used framework extensions and eventually all to level 6:
    - `mvccore/ext-form*`,
    - `mvccore/ext-model*`,
    - `mvccore/ext-controller*`,
    - `mvccore/ext-router*`.

- Tests

- Context Class
  - Equivalent class in .NET Core - HttpContext.
  - The class should contain application objects that differ in requests.
  - Everything else outside this class should be cacheable or should be able to exist in an unchanged form in memory for future implementation of asynchronous operation.
  - This will be a major change, requiring a major release.

- Dependency Injection
  - Evaluate whether it makes sense now until the framework works asynchronously.
  - Evaluate the pros of the new syntactic sugar compared to the basic mission of the framework.

- Implement asynchronous operation of the entire framework
  - See if it is possible to create a custom server using php-pm, react php, or using: https://amphp.org/.
  - Quite a nice solution at first glance:
    - https://frankenphp.dev/
  - Global PHP matters will need to be removed:
    - `setlocale()`, `default_timezone_set()`...
    - Super global variables.
  - Either implement as async extensions or implement directly into the existing code.
  - This will be a major change, requiring a major release.

- Additional extensions:
  - Session for storing sessions in the database
    - http://php.net/session_set_save_handler
  - More cache extensions:
    - Simple filesystem cache or SQLite cache
    - http://www.php-cache.com/en/latest/
  - Output cache (similar to Pimcore CMS)
  - Implementation of Latte templates.

- Examples
  - YAML configuration separately, not just as a commented-out option in another example.
  - Example for a database translator with categories.
  - Example where there are multiple domains in one application.