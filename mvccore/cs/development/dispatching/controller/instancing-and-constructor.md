# Instancování a konstruktor controlleru

## Obsah
- [**Úvod**](#úvod)
- [**Možnosti rozšíření chování**](#možnosti-rozšíření-chování)
- [**Setup klíčových properties**](#setup-klíčových-properties)

## Úvod
Interface controlleru nedefinuje žádný `__constructor()` třídy controller.
Controller je instancí aplikace vytvořen vždy přes statickou metodu `CreateInstance()`:
```php
// plný název třídy controlleru poděděný z \MvcCore\Controller
$controllerClass = '\App\Controllers\Index'; 
// vytvoření instance nového controlleru v aplikaci
$controller = $controllerClass::CreateInstance();
```

Popisovaný kód i kód níže na této stránce je k dispozici
ve třídě a metodě `\MvcCore\Application` v metodě [**`$app->CreateController()`**](https://github.com/mvccore/mvccore/blob/master/src/MvcCore/Application/Dispatching.php#L197).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Možnosti rozšíření chování
Pokud tedy controller pro svůj běh potřebuje cokoliv navíc od standardního 
chování, může si vývojář vytvořit svoji metodu `__constructor()` v implementaci
konkrétního controlleru a tam si vše navíc inicializovat do properties controlleru.
Nebo je možné podědit statickou metodu [**`\MvcCore\Controller::CreateInstance()`**](https://github.com/mvccore/mvccore/blob/master/src/MvcCore/Controller/Dispatching.php#L26) 
a i zde si implementovat vlastní, klidně i globální chování pro controllery v aplikaci.

Tato metoda je perfektní způsob, jak implementovat Dependency Injection pattern 
pro controllery, pro jejich formuláře nebo datagridy. Stejně tak pro modely, 
které nemají žádnou povinnou cestu, jak by měli být instancovány apod.

Pokud vývojář chce mít DI design pattern v MvcCore, může využít jakoukoliv DI knihovnu,
kterou inicializuje v `Bootstrap.php` a poté zde začně tvořit první instanci controlleru,
která tvoří vše ostatní.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Setup klíčových properties
Aplikace po vytvoření nové instance controlleru nastaví do controlleru 
klíčové vlastnosti pro správný běh controlleru a také pro standardní práci vývojáře.
Postupně se volají následující settery a jimy se do instance controlleru nastaví následující vlastnosti:
```php
// do controlleru se nastaví samotná instance aplikace, 
// která může být potřeba k získání dalších informací o běhu aplikace: 
$controller->SetApplication(\MvcCore\Application $application): \MvcCore\Controller;

// nastaví se rozpoznané prostředí, ve kterém aplikace běží:
$controller->SetEnvironment(\MvcCore\Environment $environment): \MvcCore\Controller;

// nastaví se vyřizovaný objekt dotazu na aplikaci, 
// který je třeba pro běžnou práci s parametry dotazu:
$controller->SetRequest(\MvcCore\Request $request): \MvcCore\Controller;

// nastaví se objekt odpovědi, do kterého controller vloží obsah k odeslání:
$controller->SetResponse(\MvcCore\Response $response): \MvcCore\Controller;

// nastaví se router, který je třeba pro správné 
// vytváření URL adres v controlleru a view:
$controller->SetRouter(\MvcCore\Router $router);: \MvcCore\Controller;
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

[▲ o úroveň výš](../README.md)

<div class="prev-next">

[◀ předchozí: **Životní cyklus controlleru**](./lifecycle.md)  
[▶ další: **Inicializace controlleru**](./initialization.md)  

</div>