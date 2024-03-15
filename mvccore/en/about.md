# About MvcCore Framework

The MvcCore framework was created in 2016 and is developed as a set of libraries 
for the following purposes:

## Development of Small Applications
Development of small or single-page applications where it is not desirable 
to use a single large framework library or too many framework libraries split 
into smaller packages. However, frameworks divided into multiple packages still 
force us to eventually install most of the libraries, which are then used 
in large application projects anyway.

Developers should be able to manage with just the core or some debugging tools 
for development. They shouldn't focus too much on tooling for small things.

## Development of Medium and Large Applications
Development of medium to large projects, where it is possible to extend the MVC 
core of the framework according to the needs of the current project and thus have 
the possibility to write any extensive application.

The framework aims not to define too large interconnections between its packages. 
A developer can work in such a way as to always be able to replace any entire 
superclass from the core with a completely custom solution and custom path. 
For example, assemble their own singleton application with their own rules from 
traits or assemble some extension of their own router, model class, session, 
controller, form... or anything else and implement some or most things in their 
own way. This is the current principle of operation of all MvcCore extensions, 
which always extend the core in this way.

In MvcCore, many common drawbacks or missing implementations from frameworks 
are solved from the MVC perspective. For example, deeper structuring of controllers 
and views, complex routing using extensions, the ability to structure application 
units into Composer packages including templates and TS/JS/CSS files with type 
binding, default datagrids, etc.

The framework also includes meticulously crafted forms in extensions, user 
authentication, caching, various configuration options, translators, debugging 
extensions, and much more.

## Quick Learning Curve, no magic features
The framework aims to use as little magic as possible, which slows down new 
developers in normal framework work until they learn specific magic in MVC, 
new templating languages, etc.

Developers should be able to open the application in the framework in an IDE, 
read basic documentation about handling and processing HTTP requests, and start 
working with the framework confidently based on their knowledge of OOP in PHP.

## Long-term PHP Support
The framework does not want to surf on transient trends and use only super 
hot PHP features. Support for the new PHP version is a matter of course. 
But MvcCore also wants to support as many older PHP versions as possible 
for greater freedom in using applications on older runtime platforms without 
the need to reconfigure the server, because that is also a reality.

Developers can, of course, write their new application in the latest PHP syntax, 
yet the framework and its examples can be used even on older PHP runtime versions.

## Development of Single-File Applications and Tools
Development of single-file MVC applications, which suffer from common drawbacks 
such as the complexity of source code or packaging into PHAR with slower 
interpretation without this framework.

The framework addresses this problem by allowing the developer to write the application 
quite conventionally except for assets and then bundle it into a single PHP or PHAR 
file with several operating modes against the file system. It is possible to stick 
with files in the package or accept files from outside or both with different priorities.

Using the MvcCore `Packager` library, it is also possible to package fully foreign 
libraries into a single PHP file, which speeds up PHP interpretation by using only 
one OPCODE from the cache and no recompilation and reinterpretation before assembling 
individual objects into the final result for processing.
