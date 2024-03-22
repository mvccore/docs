# Start aplikace

## Obsah
- [**`.htaccess` (`web.config`)**](#htaccess-webconfig)
- [**Start aplikace v `index.php`**](#start-aplikace-v-indexphp)
- [**PHP a Composer Autoloading**](#php-a-composer-autoloading)
- [**`Bootstrap.php`**](#bootstrapphp)
- [**Postup inicializace**](#postup-inicializace)

## `.htaccess` (`web.config`)

Aplikace v MvcCore standardně běží na Windows i Linuxu ve webových serverech 
Apache i IIS. Běh ve webserveru NGINX nebo přes PHP vestavěný webserver 
prozatím není implementován.

Aplikace nepotřebuje žádné speciální nastavení webserveru, pokud 
nepoužívá nějaké rozšíření, které potřebuje pro svůj běh nějakou PHP extenzi typu `Intl` apod.

Pokud je nutné pro větší aplikace používat hezké URL, je třeba mít ve webserveru 
zapnutou funkci přepisování URL adres:
- Apache mod_rewrite
- IIS - URL Rewrite

##### Příklad přepisování URL adres v Apache
```bash
RewriteEngine On

RewriteCond %{REQUEST_FILENAME} -s [OR]
RewriteCond %{REQUEST_FILENAME} -l [OR]
RewriteCond %{REQUEST_FILENAME} -f
RewriteRule ^(.*)$ $1 [NC,L]
	
RewriteRule ^.*$ index.php [NC,L]
```

##### Příklad přepisování URL adres v IIS
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <rewrite>
            <rules>
                <rule name="Rewrite all requests (not a directory or a file) to index.php" stopProcessing="true">
                    <match url="^.*$" />
                    <conditions logicalGrouping="MatchAll">
                        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
                        <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
                    </conditions>
                    <action type="Rewrite" url="index.php" />
                </rule>
            </rules>
        </rewrite>
    </system.webServer>
</configuration>

```
&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp;  

## Start aplikace v `index.php`

Aplikace v `index.php` má obvykle co nejkratší zápis:
```php
<?php
	@include_once(__DIR__ . '/../vendor/autoload.php');
	$app = \App\Bootstrap::Init();
	$app->Dispatch();
```
Řádek č. 2 je volitelný, podle toho, zda aplikace používá PHP Composer.
Pokud plánujete aplikaci zabalit do jednosouborového řešení, měl by 
první řádek mít zavináč na začátku. Důvody "proč" přesahují rámec dokumentace.
Pro standardní webové aplikace není nutné používat zavináč na začátku řádku.

Řádek č. 3 je volání jednoduché třídy `Bootstrap`, která připraví 
a nastaví novou instanci aplikace pro každý request.

Řádek č. 4 je zpracování requestu frameworkem. Zde již pokračuje 
tok aplikace třídou `\MvcCore\Application`.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## PHP a Composer Autoloading

Aplikace standardně používají PHP Composer pro PHP autoloading tříd
ze složky `./vendor`. Avšak lze použít MvcCore framework i pro 
starší vývoj aplikací bez PHP Composeru.

Pokud je v aplikaci použit PHP Composer, všechny třídy a soubory
ze složky `./vendor` jsou automaticky řešeny pouze autoloadingem 
Composeru a tento autoloading se spouští jako první v pořadí, neboť
ve složce `./vendor` je obvykle více souborů než v samotné aplikaci
a Composer si všechny souboru pamatuje v interní mapě.

PHP třídy ve složce `./App` je nutné pojmenovávat přesně podle PHP 
namespaces, aby mohl fungovat autoloading MvcCore, který je zařazen 
za autoloading Composeru. Automaticky se nejprve hledá v těchto umístěních:
- `./App`
- `./Libs`
- `.`

Umístění lze konfigurovat pomocí definice konstant `MVCCORE_*`.

Pokud tedy PHP hledá třídu `\App\Controllers\Index`, automaticky se hledá
v těchto umístěních:
- `./App/Controllers/Index.php` (nalezeno, na další se nepokračuje),  
- `./Libs/Controllers/Index.php`,  
- `./Controllers/Index.php`.  

Lze takto načíst starým způsobem i libovolnou třídu, která respektuje 
názvy složek podle PHP namespaces. Například třída `\Business\Store`
by mohla manuálně existovat v umístěních:
- `./App/Business/Store.php`,
- `./Libs/Business/Store.php`,
- `./Business/Store.php`.
Občas se může vyskytnout potřeba použít i starší PHP kód, 
který prostě nemá Composer balíček...

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## `Bootstrap.php`
Jedná se o velmi jednoduchou statickou třidu, kde se obvykle děje 
nastavení a inicializace základních objektů aplikace.

Umístění třídy je obvykle v `./App/Bootstrap.php` a třída je automaticky
načtena autoloadingem MvcCore.

Třída Bootstrap nemusí implementovat žádný interface a její stavba 
je pouze na základě zvyklostí, které říkají, že její veřejná 
statická metoda `Init()` má vrátit instanci objektu aplikace
v připraveném stavu pro vyřizování požadavku:
```php
<?php
namespace App;

use \MvcCore\Application;

class Bootstrap {
	public static function Init ():Application {

		$app = Application::GetInstance();

		// ... do something with an $app

		return $app;
	}
}

```

Pokud není třeba instanci aplikace nijak nastavovat, `Bootstrap` třída nemusí existovat.
Instance aplikace se může vyrobit v `index.php` a zde přímo volat funkci $app->Dispatch();`.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Postup inicializace
Pořadí při inicalizaci komplexnější aplikace je v některých 
případech třeba dodržet a je doporučené postupovat v tomto pořadí:

1. Vytvoření instance aplikace pomocí `\MvcCore\Application::GetInstance();`.
2. Nastavení tříd jádra (volitelné, nahrazení supertříd jádra za extenze).
3. Další nastavení jádra (volitelné, PHP 8 atributy, CSRF, výchozí controller apod...).
4. Vytvoření instance `\MvcCore\Request` (volitelné, může se hodit pro další postup).
5. Nastavení cache (volitelné).
6. Vytvoření objektu `\MvcCore\Environment` a detekce prostředí
  (volitelné, ale nutné pro další inicializace níže).
7. Načtení systémové konfigurace (volitelné, ale již podle detekovaného prostředí).
8. Nastavení autentizace uživatelů (volitelné).
9. Nastavení routeru (volitelné).
10. Ostatní nastavení výše neuvedené.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

[▲ o úroveň výš](../README.md)

<div class="prev-next">

[◀ předchozí: **Cesta zpracování v aplikaci**](./README.md)  
[▶ další: **Průchod zpracování v aplikaci**](./app-dispatch.md)  

</div>