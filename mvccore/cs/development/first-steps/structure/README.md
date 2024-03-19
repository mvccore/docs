# Struktura složek projektu

## Obsah
- [**Úvod**](#úvod)
- [**Minimální setup**](#minimální-setup)
- [**Volitelné složky**](#volitelné-složky)
- [**Úplný přehled základní struktury webové aplikace**](#úplný-přehled-základní-struktury-webové-aplikace)
- [**Rozdíly v jedno-souborových aplikacích**](#rozdíly-v-jedno-souborových-aplikacích)
- [**Soubory minimálního setupu**](#soubory-minimálního-setupu)

## Úvod
Názvy a cesty složek lze konfigurovat nastavením jádra aplikace v úvodu souboru `Bootstrap.php`.

## Minimální setup
Minimální setup adresářů a souborů pro webovou aplikaci v MvcCore vypadá následovně:  
(soubory jsou s uvozovkami pouze kvůli obarvení jinou barvou:-)
```sh
App                           # povinná, složka obsahující strukturu aplikačních PHP scriptů a šablon
    Controllers               # povinná, složka se strukturou tříd controllerů, neomezené zanoření
        'Index.php'           # povinný, výchozí controller
    Views                     # povinná, složky s podsložkami vykreslovacích šablon
        Layouts               # povinná, složka se strukturou šablon layoutů, neomezené zanoření
            'layout.phtml'    # výchozí soubor layoutové šablony
        Scripts               # povinná, složka se strukturou šablon controllerů a akcí, neomezené zanoření
            index             # povinná, složka s šablonami výchozího controlleru
                'index.phtml' # povinná, šablona výchozího controlleru a akce
                'error.phtml' # povinná, šablona chyby
    'Bootstrap.php'           # volitelná icializační třída
Var                           # volitelná, složka s dočasnými daty
    Logs                      # volitelná, složka se zalogovanými daty a vyjímkami
www                           # povinná, složka s document rootu web serveru
    'index.php'               # povinný, startovní script aplikace
vendor                        # volitelná, složka composeru s balíčky třetích stran
'composer.json'               # volitelný, config vyžadovaných balíčků třetích stran
'composer.lock'               # volitelný, config nainstalovaných balíčků třetích stran
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Volitelné složky
Standardní webová aplikace v MvcCore má obvykle rozšířenější strukturu složek.
Navíc zde mohou být následující volitelné složky, podle povahy aplikace:
```sh
'./App/Forms'         # složka se strukturou tříd formulářů,
'./App/Models'        # složka se strukturou tříd modelů,
'./App/Routers'       # složka s extendovanou třídou routeru, rout nebo jejich továren,
'./App/Views/Helpers' # složka pro vlastní pomocné funkcí šablon,
'./App/Views/Forms'   # složka se strukturou případných šablon formulářů,
'./App/config.ini'    # volitelný systémový config
'./Var/Tmp'           # složka s ostatními dočasnými aplikačními soubory,
'./www/static/css'    # složka s kaskádovými styly,
'./www/static/js'     # složka s TypeScript zdrojovými soubory (zde leží `tsconfig.json`),
'./www/static/ts'     # složka s TypeScript zdrojovými soubory (zde leží `tsconfig.json`),
'./www/static/tmp'    # složka s dočasnými skupinovými *.js soubory nebo *.d.ts soubory z jiných composer balíčků,
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Úplný přehled základní struktury webové aplikace
```sh
App                           # povinná, složka obsahující strukturu aplikačních PHP scriptů a šablon
    Controllers               # povinná, složka se strukturou tříd controllerů, neomezené zanoření
        'Index.php'           # povinný, výchozí controller
    Forms                     # volitelná, složka se strukturou tříd formulářů, neomezené zanoření
    Models                    # volitelná, složka se strukturou tříd modelů, neomezené zanoření
    Routers                   # volitelná, složka s extendovanou třídou routeru, rout nebo jejich továren
    Views                     # povinná, složky s podsložkami vykreslovacích šablon
        Helpers               # volitelná, složka pro vlastní pomocné funkcí šablon
        Forms                 # volitelná, složka se strukturou případných šablon formulářů, neomezené zanoření
        Layouts               # povinná, složka se strukturou šablon layoutů, neomezené zanoření
            'layout.phtml'    # výchozí soubor layoutové šablony
        Scripts               # povinná, složka se strukturou šablon controllerů a akcí, neomezené zanoření
            index             # povinná, složka s šablonami výchozího controlleru
                'index.phtml' # povinná, šablona výchozího controlleru a akce
                'error.phtml' # povinná, šablona chyby
    'Bootstrap.php'           # volitelná icializační třída
    'config.ini'              # volitelný systémový config
Libs                          # volitelná, složka s případně manuálně umístěnými knihovnami
Var                           # volitelná, složka s dočasnými daty
    Logs                      # volitelná, složka se zalogovanými daty a vyjímkami
    Tmp                       # volitelná, složka s ostatními dočasnými aplikačními soubory
www                           # povinná, složka s document rootu web serveru
    'index.php'               # povinný, startovní script aplikace
    static                    # volitelná, složka se statickým HTTP obsahem
        css                   # volitelná, složka s kaskádovými styly
        js                    # volitelná, složka s JavaScript soubory (možná výstupní složka pro TypeScript build)
        ts                    # volitelná, složka s TypeScript zdrojovými soubory (zde leží `tsconfig.json`)
        tmp                   # volitelná, složka s dočasnými skupinovými *.js soubory nebo *.d.ts soubory z jiných composer balíčků
vendor                        # volitelná, složka composeru s balíčky třetích stran
'composer.json'               # volitelný, config vyžadovaných balíčků třetích stran
'composer.lock'               # volitelný, config nainstalovaných balíčků třetích stran
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Rozdíly v jedno-souborových aplikacích
Struktura aplikace pro jedno-souborový projekt má pouze chybějící webserver document root `www`
a všechny jeho složky jsou přesunuty o úroveň výš. Soubor `index.php` pak navíc obsahuje 
definici konstanty `MVCCORE_APP_ROOT`, která v klasických webových projektech není uvedena.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Soubory minimálního setupu
### `./App/Controllers/Index.php`
```php
<?php

namespace App\Controllers;

class Index extends \MvcCore\Controller {

    public function IndexAction () {
		$this->view->title = 'MvcCore Aplication';
    }

	public function NotFoundAction(){
		$this->ErrorAction();
	}

	public function ErrorAction () {
		$code = $this->response->GetCode();
		if ($code === 200) $code = 500;
		$this->view->title = "Error {$code}";
		$this->view->message = $this->request->GetParam('message', FALSE);
		$this->Render('error');
	}
}
```

### `./App/Views/Layouts/layout.phtml`
```php
<?php /** @var \MvcCore\View $this */ ?><!DOCTYPE HTML>
<html lang="en-US">
	<head>
		<meta charset="UTF-8" />
		<title><?=$title?></title>
		<base href="<?=$request->GetBasePath()?>/" />
	</head>
	<body>
		<?=$this->GetContent()?>
	</body>
</html>
```

### `./App/Views/Scripts/index/index.phtml`
```php
<?php /** @var \MvcCore\View $this */ ?>
<h1><?=$title?></h1>
```

### `./App/Views/Scripts/index/error.phtml`
```php
<?php /** @var \MvcCore\View $this */ ?>
<h1><?=$title?></h1>
<pre><code><?=$escape($message)?></code></pre>
```

### `./www/index.php`
```php
<?php
	@include_once('../vendor/autoload.php');
	\App\Bootstrap::Init();
	$app->Dispatch();
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

<div class="prev-next">

[předchozí: **Založení projektu**](../new-project/README.md)  
[další: **Používání příkladů**](../examples/README.md)

</div>