# Rozšíření a repozitáře MvcCore 

## Obsah
- [**Jádro frameworku**](#jádro-frameworku)   
  - [Jádro frameworku](#jádro-frameworku)  
- [**Rozšíření - cache**](#rozšíření---cache)   
  - [Cache](#cache)  
  - [Cache - Redis](#cache---redis)  
  - [Cache - Memcached](#cache---memcached)  
  - [Cache - Memcache](#cache---memcache)  
- [**Rozšíření - konfigurace**](#rozšíření---konfigurace)   
  - [Config - Cached](#config---cached)  
  - [Config - Yaml](#config---yaml)  
- [**Rozšíření - controlery**](#rozšíření---controlery)   
  - [Controller - DataGrid](#controller---datagrid)  
  - [Controller - DataGrid - AgGrid](#controller---datagrid---aggrid)  
  - [Controller - DataGrid - AgGrid - Assets](#controller---datagrid---aggrid---assets)  
- [**Rozšíření - debugování**](#rozšíření---debugování)   
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
- [**Rozšíření - formuláře**](#rozšíření---formuláře)   
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
- [**Rozšíření - modelové formuláře**](#rozšíření---modelové-formuláře)   
  - [ModelForm](#modelform)  
- [**Rozšíření - modely**](#rozšíření---modely)   
  - [Model - Database](#model---database)  
  - [Model - Database - Microsoft SQL Server](#model---database---microsoft-sql-server)  
  - [Model - Database - MySQL/MariaDB/Percona Server](#model---database---mysqlmariadbpercona-server)  
  - [Model - Database - Postgre SQL](#model---database---postgre-sql)  
  - [Model - Database - SQLite](#model---database---sqlite)  
- [**Rozšíření - routery**](#rozšíření---routery)   
  - [Router - Extended](#router---extended)  
  - [Router - Localization](#router---localization)  
  - [Router - Media](#router---media)  
  - [Router - Media & Localization](#router---media--localization)  
  - [Router - Modules](#router---modules)  
  - [Router - Modules With Localization](#router---modules-with-localization)  
  - [Router - Modules With Media](#router---modules-with-media)  
  - [Router - Modules With Media & Localization](#router---modules-with-media--localization)  
- [**Rozšíření - překladače**](#rozšíření---překladače)   
  - [Translator](#translator)  
  - [Translator - CSV](#translator---csv)  
- [**Rozšíření - nástroje**](#rozšíření---nástroje)   
  - [Tool - CLI - Windows Fork](#tool---cli---windows-fork)  
  - [Tool - Collections](#tool---collections)  
  - [Tool - Content Security Policy](#tool---content-security-policy)  
  - [Tool - Image](#tool---image)  
  - [Tool - Locale](#tool---locale)  
  - [Tool - Locale - FloatParser](#tool---locale---floatparser)  
  - [Tool - Mimetype & Extension](#tool---mimetype--extension)  
  - [Tool - TypeScript Generator](#tool---typescript-generator)  
- [**Rozšíření - pomocné funkce šablon**](#rozšíření---pomocné-funkce-šablon)   
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
- [**Ostatní**](#ostatní)   
  - [Dokumentace](#dokumentace)  
  - [Packager](#packager)  

---


## Jádro frameworku
	
#### [Jádro frameworku](https://github.com/mvccore/mvccore)  
MvcCore je PHP MVC framework určený pro vývoj a balení projektů (částečně nebo úplně) do nadmíru rychlých aplikací a nástrojů ve formě jediného souboru.  
```sh
composer require mvccore/mvccore  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  




## Rozšíření - cache
	
#### [Cache](https://github.com/mvccore/ext-cache)  
Základní rozšíření pro rozšíření vyrovnávacích pamětí. Toto rozšíření 
je vyžadováno všemi rozšířeními vyrovnávacích pamětí a je třeba jej 
nepoužít napřímo ale jako instalaci některé konkrétní implementace vyrovnávací paměti.  
```sh
composer require mvccore/ext-cache  
```  
&nbsp;  


#### [Cache - Redis](https://github.com/mvccore/ext-cache-redis)  
Rozšíření slouží pro vytvoření připojení do vyrovnávací paměti v Redis database.  
```sh
composer require mvccore/ext-cache-redis  
```  
&nbsp;  


#### [Cache - Memcached](https://github.com/mvccore/ext-cache-memcached)  
Rozšíření slouží pro vytvoření připojení do vyrovnávací paměti v Memcached 
serveru pomocí novější implementace PHP extenze `php_memcached`.
```sh
composer require mvccore/ext-cache-memcached  
```  
&nbsp;  


#### [Cache - Memcache](https://github.com/mvccore/ext-cache-memcache)  
Rozšíření slouží pro vytvoření připojení do vyrovnávací paměti v Memcached 
serveru pomocí starší implementace PHP extenze `php_memcache`.
```sh
composer require mvccore/ext-cache-memcache  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  




## Rozšíření - konfigurace
	
#### [Config - Cached](https://github.com/mvccore/ext-config-cached)  
Rozšíření slouží pro ukládaních konfiguračních souborů do vyrovnávací paměti.  
```sh
composer require mvccore/ext-config-cached  
```  
&nbsp;  


#### [Config - Yaml](https://github.com/mvccore/ext-config-yaml)  
Rozšíření umožňuje používat syntaxi konfiguračních souborů ve formátu YAML.  
```sh
composer require mvccore/ext-config-yaml  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  




## Rozšíření - controlery
	
#### [Controller - DataGrid](https://github.com/mvccore/ext-controller-datagrid)  
Rozšíření slouží k vytváření datagrid komponenty na základě modelové třídy  
(stránkování, řazení a filtrování, HTML, pro front-end sekce aplikací).  
```sh
composer require mvccore/ext-controller-datagrid  
```  
&nbsp;  


#### [Controller - DataGrid - AgGrid](https://github.com/mvccore/ext-controller-datagrid-ag)  
Rozšíření slouží k vytváření datagrid komponenty na základě modelové třídy  
(stránkování, řazení a filtrování, HTML/CSS/TypeScript/AJAX pomocí knihovny AgGrid,  
pro back-end sekce aplikací).  
```sh
composer require mvccore/ext-controller-datagrid-ag  
```  
&nbsp;  


#### [Controller - DataGrid - AgGrid - Assets](https://github.com/mvccore/ext-controller-datagrid-ag-js)  
Rozšíření poskytuje soubory TS/JS/CSS pro předchozí datagrid komponentu s knihovnou AgGrid.
```sh
composer require mvccore/ext-controller-datagrid-ag-js  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  




## Rozšíření - debugování
	
#### [Debug - Nette Tracy - Panel Authentication](https://github.com/mvccore/ext-debug-tracy-auth)  
Vykreslí a přidá do panelu Tracy aktuálně přihlášeného uživatele z instance singleton služby  
`\MvcCore\Ext\Auth` pomocí `\Tracy\Dumper::toHtml(\MvcCore\Ext\Auth::GetInstance()->GetUser());`.  
```sh
composer require mvccore/ext-debug-tracy-auth  
```  
&nbsp;  


#### [Debug - Nette Tracy - Panel Database](https://github.com/mvccore/ext-debug-tracy-db)  
Vykreslí SQL dotazy s parametry, časy provedení a backtrace voláním.
```sh
composer require mvccore/ext-debug-tracy-db  
```  
&nbsp;  


#### [Debug - Nette Tracy - Panel MvcCore Overview](https://github.com/mvccore/ext-debug-tracy-mvccore)  
Vykreslí a přidá do panelu Tracy aktuální instanci `\MvcCore\Application`  
pomocí `\Tracy\Dumper::toHtml(\MvcCore\Application::GetInstance());`  
pro zobrazení hlavních objektů aplikace použitých k vykreslení aktuální odpovědi  
stránky. Vykreslené objekty uvnitř aplikace MvcCore:  
- `\MvcCore\Request`,
- `\MvcCore\Response`,
- `\MvcCore\Router`,
- `\MvcCore\Controller`,
Vlastní aplikační handlery a mnoho dalších interních hodnot aplikace.
```sh
composer require mvccore/ext-debug-tracy-mvccore  
```  
&nbsp;  


#### [Debug - Nette Tracy - Panel Refresh](https://github.com/mvccore/ext-debug-tracy-refresh)  
Automatické obnovení aktuálního tabu prohlížeče po změnách vybraných adresářů/souborů na serveru.  
```sh
composer require mvccore/ext-debug-tracy-refresh  
```  
&nbsp;  


#### [Debug - Nette Tracy - Panel Routing](https://github.com/mvccore/ext-debug-tracy-routing)  
Vykreslí a přidá do panelu Tracy všechny konfigurované routy v routeru  
a zobrazí aktuálně zachycenou routu.
```sh
composer require mvccore/ext-debug-tracy-routing  
```  
&nbsp;  


#### [Debug - Nette Tracy - Panel Session](https://github.com/mvccore/ext-debug-tracy-session)  
Vykreslí a přidá do panelu Tracy obsah `$_SESSION` a obsah jmenných prostorů `\MvcCore\Session`.  
```sh
composer require mvccore/ext-debug-tracy-session  
```  
&nbsp;  


#### [Debug - Nette Tracy Adapter](https://github.com/mvccore/ext-debug-tracy)  
Adapterová třída pro knihovnu Tracy.  
```sh
composer require mvccore/ext-debug-tracy  
```  
&nbsp;  


#### [Debug - Assets](https://github.com/mvccore/ext-debug-js)  
Soubory HTML/CSS/JavaScript pro vestavěný vývojový panel jádře frameworku MvcCore.  
```sh
composer require mvccore/ext-debug-js  
```  
&nbsp;  


#### [Debug - Nette Tracy - Panel Refresh - Assets](https://github.com/mvccore/ext-debug-tracy-refresh-js)  
Soubory pro extenzi automatického obnovení aktuálního tabu prohlížeče po změnách  
vybraných adresářů/souborů na serveru.  
```sh
composer require mvccore/ext-debug-tracy-refresh-js  
```  
&nbsp;  


#### [Debug - Nette Tracy Adapter - All Panels](https://github.com/mvccore/ext-debug-tracy-all)  
Adapterová třída pro knihovnu Tracy s všemi implementovanými panely pro ladění pro MvcCore.  
```sh
composer require mvccore/ext-debug-tracy-all  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  




## Rozšíření - formuláře
	
#### [Form](https://github.com/mvccore/ext-form)  
Rozšíření formuláře obsahuje základní třídy pro vytváření a renderování webových formulářů  
s ovládacími prvky HTML5, pro zpracování a ověření odeslaných uživatelských dat, pro správu  
relací formulářů pro výchozí hodnoty, pro správu chyb uživatelského vstupu a pro rozšíření  
a vývoj vlastních polí a skupin polí. Toto rozšíření je vyžadováno všemi následujícími  
formulářovými rozšířeními a je třeba jej nepoužít napřímo ale jako instalaci některého  
rozšíření formulářových polí.  
```sh
composer require mvccore/ext-form  
```  
&nbsp;  


#### [Form - Field - Button](https://github.com/mvccore/ext-form-field-button)  
Typy polí formuláře - `button:submit`, `button:reset`, `input:submit`, `input:reset` a `image`.
```sh
composer require mvccore/ext-form-field-button  
```  
&nbsp;  


#### [Form - Field - Date](https://github.com/mvccore/ext-form-field-date)  
Typy polí formuláře - `input:date`, `input:datetime-local`, `input:time`,  
`input:week` a `input:month`.  
```sh
composer require mvccore/ext-form-field-date  
```  
&nbsp;  


#### [Form - Field - File](https://github.com/mvccore/ext-form-field-file)  
Typ pole formuláře `input:file` k nahrávání souborů a validaci nahrávaných souborů.  
```sh
composer require mvccore/ext-form-field-file  
```  
&nbsp;  


#### [Form - Field - Numeric](https://github.com/mvccore/ext-form-field-numeric)  
Typy polí formuláře - `input:number` a `input:range`.  
```sh
composer require mvccore/ext-form-field-numeric  
```  
&nbsp;  


#### [Form - Field - Selection](https://github.com/mvccore/ext-form-field-selection)  
Typy polí formuláře - `select`, select pro výběr zemí, `input:checkbox`, `input:radio`,  
`input:color` a skupina checkboxů.  
```sh
composer require mvccore/ext-form-field-selection  
```  
&nbsp;  


#### [Form - Field - Text](https://github.com/mvccore/ext-form-field-text)  
Typy polí formuláře - `input:text`, `input:email`, `input:password`, `input:search`,  
`input:tel`, `input:url` a `textarea`.  
```sh
composer require mvccore/ext-form-field-text  
```  
&nbsp;  


#### [Form - Validator - Special](https://github.com/mvccore/ext-form-validator-special)  
Validátory speciálních hodnot - identifikační číslo firmy (EU), daňové identifikační  
číslo firmy (EU), platební karta, hexadecimální číslo, číslo bankovního účtu IBAN, IP adresa a PSČ (ZIP).  
```sh
composer require mvccore/ext-form-validator-special  
```  
&nbsp;  


#### [Form - Assets](https://github.com/mvccore/ext-form-js)  
Soubory CSS/JS pro vestavěné nebo vlastní ovládací prvky  
v rozšíření formulářových polí frameworku MvcCore.  
```sh
composer require mvccore/ext-form-js  
```  
&nbsp;  


#### [Form - All Fields And Validators](https://github.com/mvccore/ext-form-all)  
Rozšíření formuláře obsahující všechny balíčky formulářů pro vytváření  
a renderování webových formulářů s ovládacími prvky HTML5, pro zpracování  
a ověření odeslaných uživatelských dat, pro správu relací formulářů pro výchozí  
hodnoty, pro správu chyb uživatelského vstupu a pro rozšíření a vývoj vlastních  
polí a skupin polí.  
```sh
composer require mvccore/ext-form-all  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  




## Rozšíření - modelové formuláře
	
#### [ModelForm](https://github.com/mvccore/ext-modelform)  
Rozšíření pro vytváření formulářů pomocí dekorovaných modelových  
tříd pomocí PHP 8 atributů nebo starších PHPDocs.  
```sh
composer require mvccore/ext-modelform  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  




## Rozšíření - modely
	
#### [Model - Database](https://github.com/mvccore/ext-model-db)  
Základní rozšíření databázového modelu s abstraktními třídami a rozhraními pro práci  
s SQL dotazy v pohodlnějším API. Toto rozšíření je vyžadováno všemi rozšířeními databázových 
model a je třeba jej nepoužít napřímo ale jako instalaci některé konkrétní implementace databáze.  
```sh
composer require mvccore/ext-model-db  
```  
&nbsp;  


#### [Model - Database - Microsoft SQL Server](https://github.com/mvccore/ext-model-db-sqlsrv)  
Rozšíření pro práci s SQL dotazy v Microsoft SQL Serveru nebo ODBC v pohodlnějším API rozhraní.  
```sh
composer require mvccore/ext-model-db-sqlsrv  
```  
&nbsp;  


#### [Model - Database - MySQL/MariaDB/Percona Server](https://github.com/mvccore/ext-model-db-mysql)  
Rozšíření pro práci s SQL dotazy v MySQL, MariaDB nebo Percona Serveru v pohodlnějším API rozhraní.  
```sh
composer require mvccore/ext-model-db-mysql  
```  
&nbsp;  


#### [Model - Database - Postgre SQL](https://github.com/mvccore/ext-model-db-pgsql)  
Rozšíření pro práci s SQL dotazy v Postgre SQL Serveru v pohodlnějším API rozhraní.  
```sh
composer require mvccore/ext-model-db-pgsql  
```  
&nbsp;  


#### [Model - Database - SQLite](https://github.com/mvccore/ext-model-db-sqlite)  
Rozšíření pro práci s SQL dotazy v SQLite databázi v pohodlnějším API rozhraní.  
```sh
composer require mvccore/ext-model-db-sqlite  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  




## Rozšíření - routery
	
#### [Router - Extended](https://github.com/mvccore/ext-router-extended)  
Trait obsahující dodatečné vlastnosti, metody pro nastavování a získávání hodnot,  
které jsou potřebné pro rozšířené implementace routerů - lokalizovaných routerů  
a routerů s verze médií na stránkách. Toto rozšíření je vyžadováno všemi  
rozšířeními routerů a je třeba jej nepoužít napřímo ale jako instalaci některé  
konkrétní implementace routeru.  
```sh
composer require mvccore/ext-router-extended  
```  
&nbsp;  


#### [Router - Localization](https://github.com/mvccore/ext-router-localization)  
Rozšíření pro router s lokalizovanými stránkami pomocí jazyka nebo jazyka a místa.  
Vícejazyčné nebo jednojazyčné cesty, konfigurovatelné routy pomocí jednoduchého vzoru  
nebo pokročile jako match + reverse, pomocí metody HTTP apod.  
```sh
composer require mvccore/ext-router-localization  
```  
&nbsp;  


#### [Router - Media](https://github.com/mvccore/ext-router-media)  
Rozšíření pro router s verzí médií webových stránek (verze plná / tablet / mobilní,  
pro různé šablony/CSS/JS soubory k vykreslování, volitelně obsažené v adrese URL).  
```sh
composer require mvccore/ext-router-media  
```  
&nbsp;  


#### [Router - Media & Localization](https://github.com/mvccore/ext-router-media-localization)  
Rozšíření pro router s verzí médií webových stránek (verze: plná / tablet / mobilní,  
pro různé šablony/CSS/JS soubory k vykreslování) společně s lokalizovanými stránkami  
pomocí jazyka nebo jazyka a místa (volitelně obsažených v adrese URL na začátku).  
```sh
composer require mvccore/ext-router-media-localization  
```  
&nbsp;  


#### [Router - Modules](https://github.com/mvccore/ext-router-module)  
Rozšíření pro router s více doménami v jediné aplikaci, definovanými pomocí  
doménových tras, cílených pomocí vlastnosti modulu v URL adrese.  
```sh
composer require mvccore/ext-router-module  
```  
&nbsp;  


#### [Router - Modules With Localization](https://github.com/mvccore/ext-router-module-localization)  
Rozšíření pro router s více doménami v jediné aplikaci společně s lokalizací  
webových stránek (jazyka nebo jazyka a místa), volitelně obsažených v doméně  
nebo v adrese URL na začátku.
```sh
composer require mvccore/ext-router-module-localization  
```  
&nbsp;  


#### [Router - Modules With Media](https://github.com/mvccore/ext-router-module-media)  
Rozšíření pro router s více doménami v jediné aplikaci společně s verzí médií  
(verze: plná / tablet / mobilní, pro různé šablony/CSS/JS soubory k vykreslování),  
volitelně obsažené v doméně nebo v adrese URL na začátku.  
```sh
composer require mvccore/ext-router-module-media  
```  
&nbsp;  


#### [Router - Modules With Media & Localization](https://github.com/mvccore/ext-router-module-media-localization)  
Rozšíření pro router s více doménami v jediné aplikaci společně s verzí médií  
(verze: plná / tablet / mobilní, pro různé šablony/CSS/JS soubory k vykreslování)  
a společně s lokalizací webových stránek (jazyka nebo jazyka a místa),  
volitelně obsažených v adrese URL na začátku.  
```sh
composer require mvccore/ext-router-module-media-localization  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  




## Rozšíření - překladače
	
#### [Translator](https://github.com/mvccore/ext-translator)  
Abstraktní třída a rozhraní jako základ pro vytváření sofistikovanějších překladačů.  
```sh
composer require mvccore/ext-translator  
```  
&nbsp;  


#### [Translator - CSV](https://github.com/mvccore/ext-translator-csv)  
Jednoduchá implementace překladatele pro formát CSV.  
```sh
composer require mvccore/ext-translator-csv  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  




## Rozšíření - nástroje
	
#### [Tool - CLI - Windows Fork](https://github.com/mvccore/ext-tool-cli-winfork)  
.NET Framework 4 utilita pro vytvoření nového PHP procesu do CLI běžíciho na pozadí  
z procesu webového requestu pomocí PHP volání utility přes `shell_exec()` nebo `system()`.  
```sh
composer require mvccore/ext-tool-cli-winfork  
```  
&nbsp;  


#### [Tool - Collections](https://github.com/mvccore/ext-tool-collections)  
Rozšíření obsahující třídy kolekcí pro typované pole v PHP - `Set` a `Map`.
```sh
composer require mvccore/ext-tool-collections  
```  
&nbsp;  


#### [Tool - Content Security Policy](https://github.com/mvccore/ext-tool-csp)  
Nástroj pro snadnou obsluhu HTTP hlavičky `Content-Security-Policy`.  
```sh
composer require mvccore/ext-tool-csp  
```  
&nbsp;  


#### [Tool - Image](https://github.com/mvccore/ext-tool-image)  
Objektově orientovaný nástroj pro zpracování obrázků podporující  
mnoho populárních operací s obrázky pro web (zaoblené rohy, maska,  
zaostření, zarovnání, vyplnění a mnoho dalších).  
Pracuje v obou případech plně s PHP extenzemi `Gd` i `Imagick`.  
```sh
composer require mvccore/ext-tool-image  
```  
&nbsp;  


#### [Tool - Locale](https://github.com/mvccore/ext-tool-locale)  
Správné nastavení a získání nastavení jazykového prostředí systému  
pomocí PHP funkce `setlocale();` na jakékoli platformě operačního systému.  
```sh
composer require mvccore/ext-tool-locale  
```  
&nbsp;  


#### [Tool - Locale - FloatParser](https://github.com/mvccore/ext-tool-locale-floatparser)  
Parsování čísla s plovoucí desetinnou čárkou, automatické zjištěním desetinného oddělovače  
nebo parsování hodnoty s plovoucí desetinnou čárkou pomocí rozšíření `Intl`.  
```sh
composer require mvccore/ext-tool-locale-floatparser  
```  
&nbsp;  


#### [Tool - Mimetype & Extension](https://github.com/mvccore/ext-tool-mimetype-extension)  
Rozšíření pro získání pole řetězců MIME typů souboru podle přípony souboru  
nebo pro získání pole řetězců přípon souboru podle řetězce MIME type souboru.  
```sh
composer require mvccore/ext-tool-mimetype-extension  
```  
&nbsp;  


#### [Tool - TypeScript Generator](https://github.com/mvccore/ext-tool-ts-generator)  
Nástroj pro snadné generování TypeScript tříd, TypeScript rozhraní nebo TypeScript  
výčtových typů z PHP objektů.  
```sh
composer require mvccore/ext-tool-ts-generator  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  




## Rozšíření - pomocné funkce šablon
	
#### [View - Helper](https://github.com/mvccore/ext-view-helper)  
Abstraktní třída a rozhraní pro vytváření sofistikovanějších pomocných funkcí šablon.  
```sh
composer require mvccore/ext-view-helper  
```  
&nbsp;  


#### [View - Helper - Assets](https://github.com/mvccore/ext-view-helper-assets)  
Rozšíření pro vykreslování, minimalizaci a seskupování (nebo dočasnému ukládání) souborů  
TypeScriptů/JavaScriptů a CSS, k přípravě všech podpůrných souborů stránky pro finální zabalení  
a odeslání ke klientovi.  
```sh
composer require mvccore/ext-view-helper-assets  
```  
&nbsp;  


#### [View - Helper - Data Url](https://github.com/mvccore/ext-view-helper-dataurl)  
Rozšíření pro konverzi obsahu libovolného souboru podle zadaného relativního  
nebo absolutního umístění ve formátu data URL: `data:image/png;base64,...`.  
```sh
composer require mvccore/ext-view-helper-dataurl  
```  
&nbsp;  


#### [View - Helper - Format Date](https://github.com/mvccore/ext-view-helper-formatdate)  
Rozšíření pro formátovatování datumu nebo času pomocí PHP rozšíření `Intl` nebo pomocí staršího `strftime()`.  
```sh
composer require mvccore/ext-view-helper-formatdate  
```  
&nbsp;  


#### [View - Helper - Format Money](https://github.com/mvccore/ext-view-helper-formatmoney)  
Rozšíření pro formátovatování finančních hodnot pomocí PHP rozšíření `Intl` nebo pomocí  
konvencí lokalizace aplikace nebo pomocí explicitních nebo výchozích argumentů.  
```sh
composer require mvccore/ext-view-helper-formatmoney  
```  
&nbsp;  


#### [View - Helper - Format Number](https://github.com/mvccore/ext-view-helper-formatnumber)  
Rozšíření pro formátovatování celých nebo plovoucích čísel pomocí PHP rozšíření `Intl`  
nebo pomocí konvencí lokalizace aplikace nebo pomocí explicitních nebo výchozích argumentů.  
```sh
composer require mvccore/ext-view-helper-formatnumber  
```  
&nbsp;  


#### [View - Helper - Internationalized](https://github.com/mvccore/ext-view-helper-internationalized)  
Abstraktní třída pro zpracování formátování datumů a časů, celých a plovoucích čísel  
nebo finančích hodnot pomocí PHP rozšíření `Intl` nebo podle konvencí lokalizace aplikace.  
Toto rozšíření je vyžadováno některými ostatními rozšířeními pomocných funkcí šablon  
a je třeba ho nepoužít napřímo ale jako instalaci některého výše zmíněného rozšíření.  
```sh
composer require mvccore/ext-view-helper-internationalized  
```  
&nbsp;  


#### [View - Helper - Line Breaks](https://github.com/mvccore/ext-view-helper-linebreaks)  
Rozšíření pro správné zpracování nezalomitelných mezer ve viditelném textu.  
Možnost konfigurace pro libovolný jazyk.  
```sh
composer require mvccore/ext-view-helper-linebreaks  
```  
&nbsp;  


#### [View - Helper - Truncate](https://github.com/mvccore/ext-view-helper-truncate)  
Rozšíření pro oříznutí čistého textu nebo textu s HTML značkami na maximální počet znaků.  
```sh
composer require mvccore/ext-view-helper-truncate  
```  
&nbsp;  


#### [View - Helper - Write By JS](https://github.com/mvccore/ext-view-helper-writebyjs)  
Rozšíření pro zabránění čtení ciltivého obsahu před jednoduchými roboty pomocí převodu  
obsahu na JS kód `<script>document.write(String.fromCharCode(...));</script>`.  
```sh
composer require mvccore/ext-view-helper-writebyjs  
```  
&nbsp;  


#### [View - Helper - All](https://github.com/mvccore/ext-view-helper-all)  
Všechny rozšíření pomocných funkcí šablon frameworku MvcCore dohromady.  
```sh
composer require mvccore/ext-view-helper-all  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  




## Ostatní
	
#### [Dokumentace](https://github.com/mvccore/docs)  
Dokumentace frameworku MvcCore, dokumentace jádra, MvcCore rozšíření a balení aplikací pomocí Packageru.
```sh
composer require mvccore/docs  
```  
&nbsp;  


#### [Packager](https://github.com/mvccore/packager)  
Knihovna pro zabalení jakéhokoli typu aplikace do archivu PHAR nebo jakékoli aplikace MvcCore  
do jediného PHP souboru s extrémně rychlým výkonem nevyužívajícím bolesti reinterpretace PHP.  
```sh
composer require mvccore/packager  
```  
&nbsp;  
[↑ Obsah](#Obsah)  
&nbsp;&nbsp;  

---

[▲ o úroveň výš](../../README.md)

<div class="prev-next">

[◀ předchozí: **Vývoj aplikací**](./development/README.md)  
[▶ další: **Roadmapa budoucího vývoje**](./roadmap.md)  

</div>