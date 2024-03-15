# MvcCore Extensions And Repositories  

## Outline
- [**Framework Core**](#framework-core)   
  - [Framework Core](#framework-core)  
- [**Extensions - cache**](#extensions---cache)   
  - [Cache](#cache)  
  - [Cache - Redis](#cache---redis)  
  - [Cache - Memcached](#cache---memcached)  
  - [Cache - Memcache](#cache---memcache)  
- [**Extensions - Configs**](#extensions---configs)   
  - [Config - Cached](#config---cached)  
  - [Config - Yaml](#config---yaml)  
- [**Extensions - Controllers**](#extensions---controllers)   
  - [Controller - DataGrid](#controller---datagrid)  
  - [Controller - DataGrid - AgGrid](#controller---datagrid---aggrid)  
  - [Controller - DataGrid - AgGrid - Assets](#controller---datagrid---aggrid---assets)  
- [**Extensions - Debugging**](#extensions---debugging)   
  - [Debug - Nette Tracy - Panel Authentication](#debug---nette-tracy---panel-authentication)  
  - [Debug - Nette Tracy - Panel Database](#debug---nette-tracy---panel-database)  
  - [Debug - Nette Tracy - Panel MvcCore Overview](#debug---nette-tracy---panel-mvccore-overview)  
  - [Debug - Nette Tracy - Panel Refresh](#debug---nette-tracy---panel-refresh)  
  - [Debug - Nette Tracy - Panel Routing](#debug---nette-tracy---panel-routing)  
  - [Debug - Nette Tracy - Panel Session](#debug---nette-tracy---panel-session)  
  - [Debug - Nette Tracy Adapter](#debug---nette-tracy-adapter)  
  - [Debug - Assets](#debug---assets)  
  - [Debug - Nette Tracy - Panel Refresh - Assets](#debug---nette-tracy---panel-refresh---assets)  
  - [Debug - Nette Tracy Adapter - All Panels](#debug---nette-tracy-adapter---all-panels)  
- [**Extensions - Forms**](#extensions---forms)   
  - [Form](#form)  
  - [Form - Field - Button](#form---field---button)  
  - [Form - Field - Date](#form---field---date)  
  - [Form - Field - File](#form---field---file)  
  - [Form - Field - Numeric](#form---field---numeric)  
  - [Form - Field - Selection](#form---field---selection)  
  - [Form - Field - Text](#form---field---text)  
  - [Form - Validator - Special](#form---validator---special)  
  - [Form - Assets](#form---assets)  
  - [Form - All Fields And Validators](#form---all-fields-and-validators)  
- [**Extensions - Model Forms**](#extensions---model-forms)   
  - [ModelForm](#modelform)  
- [**Extensions - Models**](#extensions---models)   
  - [Model - Database](#model---database)  
  - [Model - Database - Microsoft SQL Server](#model---database---microsoft-sql-server)  
  - [Model - Database - MySQL/MariaDB/Percona Server](#model---database---mysql/mariadb/percona-server)  
  - [Model - Database - Postgre SQL](#model---database---postgre-sql)  
  - [Model - Database - SQLite](#model---database---sqlite)  
- [**Extensions - Routers**](#extensions---routers)   
  - [Router - Extended](#router---extended)  
  - [Router - Localization](#router---localization)  
  - [Router - Media](#router---media)  
  - [Router - Media & Localization](#router---media--localization)  
  - [Router - Modules](#router---modules)  
  - [Router - Modules With Localization](#router---modules-with-localization)  
  - [Router - Modules With Media](#router---modules-with-media)  
  - [Router - Modules With Media & Localization](#router---modules-with-media--localization)  
- [**Extensions - Translators**](#extensions---translators)   
  - [Translator](#translator)  
  - [Translator - CSV](#translator---csv)  
- [**Extensions - Tools**](#extensions---tools)   
  - [Tool - CLI - Windows Fork](#tool---cli---windows-fork)  
  - [Tool - Collections](#tool---collections)  
  - [Tool - Content Security Policy](#tool---content-security-policy)  
  - [Tool - Image](#tool---image)  
  - [Tool - Locale](#tool---locale)  
  - [Tool - Locale - FloatParser](#tool---locale---floatparser)  
  - [Tool - Mimetype & Extension](#tool---mimetype--extension)  
  - [Tool - TypeScript Generator](#tool---typescript-generator)  
- [**Extensions - View Helpers**](#extensions---view-helpers)   
  - [View - Helper](#view---helper)  
  - [View - Helper - Assets](#view---helper---assets)  
  - [View - Helper - Data Url](#view---helper---data-url)  
  - [View - Helper - Format Date](#view---helper---format-date)  
  - [View - Helper - Format Money](#view---helper---format-money)  
  - [View - Helper - Format Number](#view---helper---format-number)  
  - [View - Helper - Internationalized](#view---helper---internationalized)  
  - [View - Helper - Line Breaks](#view---helper---line-breaks)  
  - [View - Helper - Truncate](#view---helper---truncate)  
  - [View - Helper - Write By JS](#view---helper---write-by-js)  
  - [View - Helper - All](#view---helper---all)  
- [**Other**](#other)   
  - [Documentation](#documentation)  
  - [Packager](#packager)  

---


## Framework Core
	
#### [Framework Core](https://github.com/mvccore/mvccore)  
MvcCore is PHP MVC framework to develop and pack projects (partialy or completely) into super fast single file apps and tools.  
```sh
composer require mvccore/mvccore  
```  
&nbsp;  
[↑ Outline](#outline)  
&nbsp;&nbsp;  




## Rozšíření - cache
	
#### [Cache](https://github.com/mvccore/ext-cache)  
Register and use cache instances, cache interface.  
```sh
composer require mvccore/ext-cache  
```  
&nbsp;  


#### [Cache - Redis](https://github.com/mvccore/ext-cache-redis)  
Cache implementation extension for Redis.  
```sh
composer require mvccore/ext-cache-redis  
```  
&nbsp;  


#### [Cache - Memcached](https://github.com/mvccore/ext-cache-memcached)  
Cache implementation extension for Memcached.  
```sh
composer require mvccore/ext-cache-memcached  
```  
&nbsp;  


#### [Cache - Memcache](https://github.com/mvccore/ext-cache-memcache)  
Cache implementation extension for Memcache (older).  
```sh
composer require mvccore/ext-cache-memcache  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp; 




## Extensions - Configs
	
#### [Config - Cached](https://github.com/mvccore/ext-config-cached)  
Extension to use cached configuration files across multiple environments.  
```sh
composer require mvccore/ext-config-cached  
```  
&nbsp;  


#### [Config - Yaml](https://github.com/mvccore/ext-config-yaml)  
Extension to have YAML configuration files syntax.  
```sh
composer require mvccore/ext-config-yaml  
```  
&nbsp;  
[↑ Outline](#outline)  
&nbsp;&nbsp;  




## Extensions - Controllers
	
#### [Controller - DataGrid](https://github.com/mvccore/ext-controller-datagrid)  
Extension to define and render datagrid component by model class.  
```sh
composer require mvccore/ext-controller-datagrid  
```  
&nbsp;  


#### [Controller - DataGrid - AgGrid](https://github.com/mvccore/ext-controller-datagrid-ag)  
Extension for administration environments with AgGrid JS/TS front-end.  
```sh
composer require mvccore/ext-controller-datagrid-ag  
```  
&nbsp;  


#### [Controller - DataGrid - AgGrid - Assets](https://github.com/mvccore/ext-controller-datagrid-ag-js)  
Extension for administration environments with AgGrid JS/TS front-end - assets files.  
```sh
composer require mvccore/ext-controller-datagrid-ag-js  
```  
&nbsp;  
[↑ Outline](#outline)  
&nbsp;&nbsp;  




## Extensions - Debugging
	
#### [Debug - Nette Tracy - Panel Authentication](https://github.com/mvccore/ext-debug-tracy-auth)  
Render and add into tracy debug panel currently authenticated user from \MvcCore\Ext\Auth service singleton instance, printed by \Tracy\Dumper::toHtml(\MvcCore\Ext\Auth::GetInstance()->GetUser());.  
```sh
composer require mvccore/ext-debug-tracy-auth  
```  
&nbsp;  


#### [Debug - Nette Tracy - Panel Database](https://github.com/mvccore/ext-debug-tracy-db)  
Render queries with params and execution times.  
```sh
composer require mvccore/ext-debug-tracy-db  
```  
&nbsp;  


#### [Debug - Nette Tracy - Panel MvcCore Overview](https://github.com/mvccore/ext-debug-tracy-mvccore)  
Render and add into tracy debug panel current MvcCore application instance, printed by \Tracy\Dumper::toHtml(\MvcCore\Application::GetInstance()); to display main application objects used to render current page response. Rendered objects inside MvcCore app: \MvcCore\Request, \MvcCore\Response, \MvcCore\Router, \MvcCore\Controller, all preroute, predispatch and postdispatch configured handlers and many other internal application values.  
```sh
composer require mvccore/ext-debug-tracy-mvccore  
```  
&nbsp;  


#### [Debug - Nette Tracy - Panel Refresh](https://github.com/mvccore/ext-debug-tracy-refresh)  
Automatic refresh of current browser tab on selected server directory changes.  
```sh
composer require mvccore/ext-debug-tracy-refresh  
```  
&nbsp;  


#### [Debug - Nette Tracy - Panel Routing](https://github.com/mvccore/ext-debug-tracy-routing)  
Render and add into tracy debug panel all configured routes in current MvcCore application router and to display currently matched route.  
```sh
composer require mvccore/ext-debug-tracy-routing  
```  
&nbsp;  


#### [Debug - Nette Tracy - Panel Session](https://github.com/mvccore/ext-debug-tracy-session)  
Render and add into tracy debug panel $_SESSION content and \MvcCore\Session namespaces content.  
```sh
composer require mvccore/ext-debug-tracy-session  
```  
&nbsp;  


#### [Debug - Nette Tracy Adapter](https://github.com/mvccore/ext-debug-tracy)  
Adapter class for Nette Framework `tracy/tracy` library.  
```sh
composer require mvccore/ext-debug-tracy  
```  
&nbsp;  


#### [Debug - Assets](https://github.com/mvccore/ext-debug-js)  
HTML/CSS/JavaScript assets for MvcCore built in debug bar.  
```sh
composer require mvccore/ext-debug-js  
```  
&nbsp;  


#### [Debug - Nette Tracy - Panel Refresh - Assets](https://github.com/mvccore/ext-debug-tracy-refresh-js)  
Automatic refresh of current browser tab on selected server directory changes - assets files.  
```sh
composer require mvccore/ext-debug-tracy-refresh-js  
```  
&nbsp;  


#### [Debug - Nette Tracy Adapter - All Panels](https://github.com/mvccore/ext-debug-tracy-all)  
Adapter class for Nette Framework `tracy/tracy` library with all implemented debug panels for MvcCore.  
```sh
composer require mvccore/ext-debug-tracy-all  
```  
&nbsp;  
[↑ Outline](#outline)  
&nbsp;&nbsp;  




## Extensions - Forms
	
#### [Form](https://github.com/mvccore/ext-form)  
Form extension with base classes to create and render web forms with HTML5 controls, 
to handle and validate submited user data, to manage forms sessions for default values, 
to manage user input errors and to extend and develop custom fields and field groups.  
```sh
composer require mvccore/ext-form  
```  
&nbsp;  


#### [Form - Field - Button](https://github.com/mvccore/ext-form-field-button)  
Form field types - button:submit, button:reset, input:submit, input:reset and image.  
```sh
composer require mvccore/ext-form-field-button  
```  
&nbsp;  


#### [Form - Field - Date](https://github.com/mvccore/ext-form-field-date)  
Form field types - input:date, input:datetime-local, input:time, input:week and input:month.  
```sh
composer require mvccore/ext-form-field-date  
```  
&nbsp;  


#### [Form - Field - File](https://github.com/mvccore/ext-form-field-file)  
Form field input:file to upload file(s) and uploaded files validation.  
```sh
composer require mvccore/ext-form-field-file  
```  
&nbsp;  


#### [Form - Field - Numeric](https://github.com/mvccore/ext-form-field-numeric)  
Form field types - input:number and input:range.  
```sh
composer require mvccore/ext-form-field-numeric  
```  
&nbsp;  


#### [Form - Field - Selection](https://github.com/mvccore/ext-form-field-selection)  
Form field types - select, country select, checkbox, radio button, color and checkboxes group.  
```sh
composer require mvccore/ext-form-field-selection  
```  
&nbsp;  


#### [Form - Field - Text](https://github.com/mvccore/ext-form-field-text)  
Form field types - input:text, input:email, input:password, input:search, input:tel, input:url and textarea.  
```sh
composer require mvccore/ext-form-field-text  
```  
&nbsp;  


#### [Form - Validator - Special](https://github.com/mvccore/ext-form-validator-special)  
Form special text and numeric validators - company ID (EU), company VAT ID (EU), credit card, hexadecimal number, IBAN bank account number, IP address and ZIP code.  
```sh
composer require mvccore/ext-form-validator-special  
```  
&nbsp;  


#### [Form - Assets](https://github.com/mvccore/ext-form-js)  
Create custom handling javascripts for any build-in or custom control in PHP forms library MvcCore Form extension.  
```sh
composer require mvccore/ext-form-js  
```  
&nbsp;  


#### [Form - All Fields And Validators](https://github.com/mvccore/ext-form-all)  
Form extension with with all form packages to create and render web forms with HTML5 controls, to handle and validate submited user data, to manage forms sessions for default values, to manage user input errors and to extend and develop custom fields and field groups.  
```sh
composer require mvccore/ext-form-all  
```  
&nbsp;  
[↑ Outline](#outline)  
&nbsp;&nbsp;  




## Extensions - Model Forms
	
#### [ModelForm](https://github.com/mvccore/ext-modelform)  
Extension to build forms by decorated model classes.  
```sh
composer require mvccore/ext-modelform  
```  
&nbsp;  
[↑ Outline](#outline)  
&nbsp;&nbsp;  




## Extensions - Models
	
#### [Model - Database](https://github.com/mvccore/ext-model-db)  
Base model database extension with base and abstract classes and interfaces to work with sql queries in more comfortable API.  
```sh
composer require mvccore/ext-model-db  
```  
&nbsp;  


#### [Model - Database - Microsoft SQL Server](https://github.com/mvccore/ext-model-db-sqlsrv)  
Extension to work with Microsoft Sql Server queries in more comfortable API.  
```sh
composer require mvccore/ext-model-db-sqlsrv  
```  
&nbsp;  


#### [Model - Database - MySQL/MariaDB/Percona Server](https://github.com/mvccore/ext-model-db-mysql)  
Extension to work with MySQL/MariaDB/Percona Server SQL queries in more comfortable API.  
```sh
composer require mvccore/ext-model-db-mysql  
```  
&nbsp;  


#### [Model - Database - Postgre SQL](https://github.com/mvccore/ext-model-db-pgsql)  
Extension to work with Postgres SQL queries in more comfortable API.  
```sh
composer require mvccore/ext-model-db-pgsql  
```  
&nbsp;  


#### [Model - Database - SQLite](https://github.com/mvccore/ext-model-db-sqlite)  
Extension to work with SQLite queries in more comfortable API.  
```sh
composer require mvccore/ext-model-db-sqlite  
```  
&nbsp;  
[↑ Outline](#outline)  
&nbsp;&nbsp;  




## Extensions - Routers
	
#### [Router - Extended](https://github.com/mvccore/ext-router-extended)  
Trait with additional properties, setters and getters required by extended router implementations - localized router and media site version router.  
```sh
composer require mvccore/ext-router-extended  
```  
&nbsp;  


#### [Router - Localization](https://github.com/mvccore/ext-router-localization)  
Extension to manage website localizations (language or language and locale). For any HTTP method, multi or single language (or lang. with locale) patterns and reverses, URL addresses like `/en/path` or `/en-US/path`, very configurable.  
```sh
composer require mvccore/ext-router-localization  
```  
&nbsp;  


#### [Router - Media](https://github.com/mvccore/ext-router-media)  
Extension to manage website media versions (full/tablet/mobile) for different templates/CSS/JS files rendering, optionally contained in URL address in the beginning.  
```sh
composer require mvccore/ext-router-media  
```  
&nbsp;  


#### [Router - Media & Localization](https://github.com/mvccore/ext-router-media-localization)  
Extension to manage website media versions (full/tablet/mobile) for different templates/CSS/JS files rendering and to manage website localizations (language or language and locale), optionaly contained in url address in the beinning.  
```sh
composer require mvccore/ext-router-media-localization  
```  
&nbsp;  


#### [Router - Modules](https://github.com/mvccore/ext-router-module)  
Extension to manage multiple websites in a single project, defined by domain routes, targeted by module property in URL completing.  
```sh
composer require mvccore/ext-router-module  
```  
&nbsp;  


#### [Router - Modules With Localization](https://github.com/mvccore/ext-router-module-localization)  
Extension to manage multiple websites in a single project and to manage website localizations (language or language and locale), optionaly contained in a domain or in URL address in the beginning.  
```sh
composer require mvccore/ext-router-module-localization  
```  
&nbsp;  


#### [Router - Modules With Media](https://github.com/mvccore/ext-router-module-media)  
Extension to manage multiple websites in a single project and to manage website media versions (full/tablet/mobile) for different templates/CSS/JS files rendering, optionally contained in a domain or in URL address in the beginning.  
```sh
composer require mvccore/ext-router-module-media  
```  
&nbsp;  


#### [Router - Modules With Media & Localization](https://github.com/mvccore/ext-router-module-media-localization)  
Extension to manage multiple websites in a single project, to manage website media versions (full/tablet/mobile) for different templates/CSS/JS files rendering and to manage website localizations (language or language and locale), optionally contained in URL address in the beginning.  
```sh
composer require mvccore/ext-router-module-media-localization  
```  
&nbsp;  
[↑ Outline](#outline)  
&nbsp;&nbsp;  




## Extensions - Translators
	
#### [Translator](https://github.com/mvccore/ext-translator)  
Abstract class and interface support code to create more sofisticated translators.  
```sh
composer require mvccore/ext-translator  
```  
&nbsp;  


#### [Translator - CSV](https://github.com/mvccore/ext-translator-csv)  
Simple CSV translator implementation.  
```sh
composer require mvccore/ext-translator-csv  
```  
&nbsp;  
[↑ Outline](#outline)  
&nbsp;&nbsp;  




## Extensions - Tools
	
#### [Tool - CLI - Windows Fork](https://github.com/mvccore/ext-tool-cli-winfork)  
.NET Framework 4 utility for CLI calls via PHP `shell_exec()` or `system()` to fork new process in background.  
```sh
composer require mvccore/ext-tool-cli-winfork  
```  
&nbsp;  


#### [Tool - Collections](https://github.com/mvccore/ext-tool-collections)  
Extension with collection classes for typed arrays in PHP.  
```sh
composer require mvccore/ext-tool-collections  
```  
&nbsp;  


#### [Tool - Content Security Policy](https://github.com/mvccore/ext-tool-csp)  
Utility to easilly complete `Content-Security-Policy` HTTP header.  
```sh
composer require mvccore/ext-tool-csp  
```  
&nbsp;  


#### [Tool - Image](https://github.com/mvccore/ext-tool-image)  
Object oriented image processing tool supporting many popular web images operations.  
```sh
composer require mvccore/ext-tool-image  
```  
&nbsp;  


#### [Tool - Locale](https://github.com/mvccore/ext-tool-locale)  
Properly set and get system locale settings by PHP ` setlocale();` across any system platform.  
```sh
composer require mvccore/ext-tool-locale  
```  
&nbsp;  


#### [Tool - Locale - FloatParser](https://github.com/mvccore/ext-tool-locale-floatparser)  
Parse float by automatic floating point detection or parse float value by `Intl` extension.  
```sh
composer require mvccore/ext-tool-locale-floatparser  
```  
&nbsp;  


#### [Tool - Mimetype & Extension](https://github.com/mvccore/ext-tool-mimetype-extension)  
Extension to get file mimetype(s) strings array from file extension string or to get file extension(s) strings array from file mimetype string.  
```sh
composer require mvccore/ext-tool-mimetype-extension  
```  
&nbsp;  


#### [Tool - TypeScript Generator](https://github.com/mvccore/ext-tool-ts-generator)  
Utility to easilly generate TypeScript classes, interfaces or enums from PHP equivalents.  
```sh
composer require mvccore/ext-tool-ts-generator  
```  
&nbsp;  
[↑ Outline](#outline)  
&nbsp;&nbsp;  




## Extensions - View Helpers
	
#### [View - Helper](https://github.com/mvccore/ext-view-helper)  
Abstract class code and interface support code to create more sofisticated view helpers with better setup and protected properties.  
```sh
composer require mvccore/ext-view-helper  
```  
&nbsp;  


#### [View - Helper - Assets](https://github.com/mvccore/ext-view-helper-assets)  
To group, render, minify and cache javascript and css files, to prepare all application assets for final application packing.  
```sh
composer require mvccore/ext-view-helper-assets  
```  
&nbsp;  


#### [View - Helper - Data Url](https://github.com/mvccore/ext-view-helper-dataurl)  
Get any file content by given relative or absolute path in data url format: `data:image/png;base64,iVBOR..`.  
```sh
composer require mvccore/ext-view-helper-dataurl  
```  
&nbsp;  


#### [View - Helper - Format Date](https://github.com/mvccore/ext-view-helper-formatdate)  
Format given date or time by `Intl` extension or by `strftime()` as fallback.  
```sh
composer require mvccore/ext-view-helper-formatdate  
```  
&nbsp;  


#### [View - Helper - Format Money](https://github.com/mvccore/ext-view-helper-formatmoney)  
Format money by `Intl` extension or by locale formating conventions or by explicit or default arguments.  
```sh
composer require mvccore/ext-view-helper-formatmoney  
```  
&nbsp;  


#### [View - Helper - Format Number](https://github.com/mvccore/ext-view-helper-formatnumber)  
Format number by `Intl` extension or by locale formating conventions or by explicit or default arguments.  
```sh
composer require mvccore/ext-view-helper-formatnumber  
```  
&nbsp;  


#### [View - Helper - Internationalized](https://github.com/mvccore/ext-view-helper-internationalized)  
Abstract class to process date, number or money formating by `Intl` extension or by locale formating conventions.  
```sh
composer require mvccore/ext-view-helper-internationalized  
```  
&nbsp;  


#### [View - Helper - Line Breaks](https://github.com/mvccore/ext-view-helper-linebreaks)  
Processing any visible text content for non-line breaking spaces.  
```sh
composer require mvccore/ext-view-helper-linebreaks  
```  
&nbsp;  


#### [View - Helper - Truncate](https://github.com/mvccore/ext-view-helper-truncate)  
Truncate plain text or text with html tags to max. chars.  
```sh
composer require mvccore/ext-view-helper-truncate  
```  
&nbsp;  


#### [View - Helper - Write By JS](https://github.com/mvccore/ext-view-helper-writebyjs)  
Prevent sensitive content against spam bots and convert content into JS `<script>document.write(String.fromCharCode(...));`</script>.  
```sh
composer require mvccore/ext-view-helper-writebyjs  
```  
&nbsp;  


#### [View - Helper - All](https://github.com/mvccore/ext-view-helper-all)  
All MvcCore View Helper extensins together.  
```sh
composer require mvccore/ext-view-helper-all  
```  
&nbsp;  
[↑ Outline](#outline)  
&nbsp;&nbsp;  




## Other
	
#### [Documentation](https://github.com/mvccore/docs)  
MvcCore - Documentation - framework and extensions documentation.  
```sh
composer require mvccore/docs  
```  
&nbsp;  


#### [Packager](https://github.com/mvccore/packager)  
Library to pack any type of app into PHAR archive or any MvcCore app into single PHP super fast result file.  
```sh
composer require mvccore/packager  
```  
&nbsp;  
[↑ Outline](#outline)  
&nbsp;&nbsp;  
