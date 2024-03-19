# Módy renderování

## Obsah
- [**Úvod**](#úvod)
- [**Nastavení módu renderování**](#nastavení-módu-renderování)
- [**1. Renderování přes output buffering do `\MvcCore\Response`**](#1-renderování-přes-output-buffering-do-MvcCoreResponse)
  - [**1.1. Vykreslení šablony akce**](#11-vykreslení-šablony-akce)
  - [**1.2. Vykreslení šablony layoutu (se stringem šablony akce)**](#12-vykreslení-šablony-layoutu-se-stringem-šablony-akce)
  - [**1.3. Uložení spojené odpovědi do objektu `\MvcCore\Response`**](#13-uložení-spojené-odpovědi-do-objektu-mvccoreresponse)
  - [**1.4. Ukončení odpovědi - odeslání HTTP hlaviček a těla odpovědi z `\MvcCore\Response`**](#14-ukončení-odpovědi---odeslání-http-hlaviček-a-těla-odpovědi-z-mvccoreresponse)
- [**2. Přímé renderování na výstup**](#2-přímé-renderování-na-výstup)
  - [**2.1. Odeslání HTTP hlaviček z `\MvcCore\Response`**](#21-odeslání-http-hlaviček-z-mvccoreresponse)
  - [**2.2. Započetí vykreslování layoutu do výstupu**](#22-započetí-vykreslování-layoutu-do-výstupu)
  - [**2.3. Vykreslení šablony akce do výstupu**](#23-vykreslení-šablony-akce-do-výstupu)
  - [**2.4. Dokončení vykreslování layoutu do výstupu**](#24-dokončení-vykreslování-layoutu-do-výstupu)
  - [**2.5. Ukončení odpovědi**](#25-ukončení-odpovědi)

## Úvod

V MvcCore je možné renderovat vykreslovací šablony dvěma vykreslovacími módy:
1. Renderování přes output buffering do `\MvcCore\Response` (výchozí).
2. Přímé renderování na výstup.

Zmíněné módy renderování jsou používány pro renderování vykreslovacích šablon,  
které se mohou používat pro typy odpovědí TEXT, HTML nebo XML.  
Módy renderování se netýkají odesílání JSON nebo JSONp odpovědí.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Nastavení módu renderování
Vykreslovací mód lze nastavit pro:
- veškeré odpovědi aplikace - v základním controlleru, 
- pro konkrétní controller - ve třídě aktuálního controlleru,
- pro konkrétní odpověď akce - v metodě akce,  

pomocí vlastnosti controlleru `$controller->renderMode;`.
Tato vlastnost může nabývat dvou hodnot:
```php
class Base extends \MvcCore\Controller {
	// 1. Renderování přes output buffering do `\MvcCore\Response` (výchozí)
	protected $renderMode = \MvcCore\IView::RENDER_WITH_OB_FROM_ACTION_TO_LAYOUT;
	
	// 2. Přímé renderování na výstup.
	protected $renderMode = \MvcCore\IView::RENDER_WITHOUT_OB_CONTINUOUSLY;
}
```

Základní controller má také setter metodu `$controller->SetRenderMode();`.

Mód renderování je možné měnit před započetím renderování první šablony.  
Jakmile je renderování započnuto, nelze ho již měnit a míchat tyto dva způsoby najednou.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## 1. Renderování přes output buffering do `\MvcCore\Response`
Tento způsob renderování je výchozím stavem a slouží pro vykreslování odpovědi většiny akcí aplikací,  
neboť většinou se nejedná o obrovské množství dat, které by bylo nutné zasílat uživateli jako stream.

Tento výstupní mód funguje v těchto krocích:
- 1.1. Vykreslení šablony akce
- 1.2. Vykreslení šablony layoutu (se stringem šablony akce)
- 1.3. Uložení spojené odpovědi do objektu `\MvcCore\Response`
- 1.4. Ukončení odpovědi - odeslání HTTP hlaviček a těla odpovědi z `\MvcCore\Response`

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### 1.1. Vykreslení šablony akce
První šablona, která se vykreslí je šablona akce controlleru.  
Vykreslování probíhá jako PHP volání v kontextu objektu   
`$controller->view`, což je instance třídy `\MvcCore\View`:
```php
// kód je pouze ilustrativní:
ob_start();
include('.../index.phtml');
$output = ob_get_clean();
```

Příklad akce v controlleru, šablony akce a jejího výstupu:
```php
// ./App/Controllers/Index.php:
class Index extends \Mvccore\Controller {
	public function IndexAction () {
		$this->view->title = 'Views playground';
		$this->view->heading = 'Hi there!';
	}
}

// ./App/Views/Scripts/index/index.phtml:
<h1><?=$heading?></h1>

// výstup vykreslení šablony akce:
'<h1>Hi there!</h1>'
```
Tento výstupní string z šablony akce se uloží do další šablony layoutu jako interní proměnná `$content`.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### 1.2. Vykreslení šablony layoutu (se stringem šablony akce)
Pokud není aplikace nastavena jinak, výchozím chováním je obalení každého výsledku akce šablonou layoutu.
Vykreslovací šablona layoutu je další nová instance třídy `\MvcCore\View`, která převezme totožný sklad 
proměnných z šablony akce a vykreslí kolem výstupu akce další obsah.

Příklad šablony layoutu a celkového výstupu (těla odpovědi aplikace):
```php
// ./App/Views/Layouts/layout.phtml:
<!DOCTYPE HTML>
<html lang="en-US">
	<head>
		<meta charset="UTF-8" />
		<title><?=$title?></title>
	</head>
	<body>
		<?=$this->GetContent()?>
	</body>
</html>

// celkový výstup (tělo odpovědi aplikace):
'<!DOCTYPE HTML>
<html lang="en-US">
	<head>
		<meta charset="UTF-8" />
		<title>Views playground</title>
	</head>
	<body>
		<h1>Hi there!</h1>
	</body>
</html>'
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### 1.3. Uložení spojené odpovědi do objektu `\MvcCore\Response`
Takto zkompletované tělo odpovědi jako vykreslená šablona akce obalená vykreslenou šablonou layoutu 
se uloží do objektu \MvcCore\Response v controlleru pro odeslání ke klientovi později v okamžku ukončování požadavku:
```php
// kód je pouze ilustrativní:
$controller->response
	->SetHeader('Content-Type', 'text/html; charset=utf-8')
	->SetCode(\MvcCore\IResponse::OK)
	->SetBody($output);
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### 1.4. Ukončení odpovědi - odeslání HTTP hlaviček a těla odpovědi z `\MvcCore\Response`
Jakmile dojde k ukončení požadavku aplikace pomocí metody `$app->Terminate()`,
dochází k odeslání HTTP hlaviček a k odeslání obsahu z objektu `\MvcCore\Response`
a vyprázdnění všech výstupních bufferů. V tu chvíli teprve skutečně dostane klient svoji odpověď.
Více o ukončování požadavku v sekci [**Průchod zpracování v aplikaci**](../application/app-dispatch.md#ukončení-požadavku-a-odeslání-odpovědi).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp;  

## 2. Přímé renderování na výstup
Tento způsob renderování slouží pro postupné odesílání obsahu k uživateli jako stream.
Využívá se pro extrémně dlouhé odpovědi, kdy je nutné postupně iterovat a odesílat data 
k uživateli tak, aby nedošlo k přetečení paměti na serveru.

Tento výstupní mód funguje v těchto krocích:
- 2.1. Odeslání HTTP hlaviček z `\MvcCore\Response`
- 2.2. Započetí vykreslování layoutu do výstupu
- 2.3. Vykreslení šablony akce do výstupu
- 2.4. Dokončení vykreslování layoutu do výstupu
- 2.5. Ukončení odpovědi

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### 2.1. Odeslání HTTP hlaviček z `\MvcCore\Response`
Protože se při renderování zapisuje přímo do výstupu, jsou jako první odeslány HTTP hlavičky.
V tomto renderovacím módu tedy vůbec nedochází k naplnění těla požadavku v objektu `\MvcCore\Response`
a objekt se využívá pouze na management HTTP hlaviček.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### 2.2. Započetí vykreslování layoutu do výstupu
Poté se začne standardním způsobem vypisovat do výstupu tělo odpovědi
od začátku souboru `layout.phtml` do okamžiku volání funkce `<?=$this->GetContent()?>`.

Příklad akce controlleru a vykreslení šablony layoutu do výstupu do započetí vykreslování šablony akce:
```php
// ./App/Controllers/Index.php:
class Index extends \Mvccore\Controller {
	public function IndexAction () {
		$this->view->title = 'Views playground';
		$this->view->heading = 'Hi there!';
	}
}

// ./App/Views/Layouts/layout.phtml:
<!DOCTYPE HTML>
<html lang="en-US">
	<head>
		<meta charset="UTF-8" />
		<title><?=$title?></title>
	</head>
	<body>
		<?=$this->GetContent()?>
	</body>
</html>

// do těla odpovědi se vypíše:
'<!DOCTYPE HTML>
<html lang="en-US">
	<head>
		<meta charset="UTF-8" />
		<title>Views playground</title>
	</head>
	<body>
		'
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### 2.3. Vykreslení šablony akce do výstupu
Jakmile dojde k volání metody `<?=$this->GetContent()?>` v objektu `\MvcCore\View` layoutu
při tomto módu renderování, nevypíše se žádný obsah interní proměnné `$content` tak jako 
v předchozím případě, ale vytvoří se další instance `\MvcCore\View`, která slouží pro 
vykreslení šablony akce. Toto další view akce převezmě od layoutu totožný sklad 
proměnných a vyrenderuje šablonu akce do těla odpovědi.

Příklad šablony akce a jejího výstupu:
```php
// ./App/Views/Scripts/index/index.phtml:
<h1><?=$heading?></h1>

// obsah šablony akce vykreslený do výstupu aplikace:
'<h1>Hi there!</h1>'
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### 2.4. Dokončení vykreslování layoutu do výstupu
Poté se dokončí vykreslení šablony layoutu od volání `<?=$this->GetContent()?>` 
v objektu `\MvcCore\View` layoutu do konce šablony layoutu.
```php
// ./App/Views/Layouts/layout.phtml:
<!DOCTYPE HTML>
<html lang="en-US">
	<head>
		<meta charset="UTF-8" />
		<title><?=$title?></title>
	</head>
	<body>
		<?=$this->GetContent()?>
	</body>
</html>

// do těla odpovědi se vypíše:
		'
	</body>
</html>'
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### 2.5. Ukončení odpovědi
Po dokončení postupného renderování všech šablon se provedou standardní ukončovací akce:
- zavolají se post dispatch handlery,
- zavolají se post terminate handlery,
- uloží se sezení v handleru zaregistrovaném přes `register_shutdown_function()`.

Více o ukončování požadavku v sekci [**Průchod zpracování v aplikaci**](../application/app-dispatch.md#ukončení-požadavku-a-odeslání-odpovědi).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

<div class="prev-next">

[předchozí: **Renderování a odesílání**](./README.md)  
[další: **Renderování šablon**](views-rendering.md/)  

</div>