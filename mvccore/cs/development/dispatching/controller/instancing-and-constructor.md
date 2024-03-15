# Instancování a konstruktor controlleru

Interface controlleru nedefinuje žádný `__constructor()` třídy controller.
Controller je instancí aplikace vytvořen vždy přes staickou metodu `CreateInstance()`:
```php
// plný název třídy controlleru poděděný z \MvcCore\Controller
$controllerClass = '\App\Controllers\Index'; 
// vytvoření instance nového controlleru v aplikaci
$controller = $controllerClass::CreateInstance();
```

Popisovaný kód i kód níže na této stránce je k dispozici
ve třídě a mětodě `\MvcCore\Application` v metodě [**`$app->CreateController()`**](https://github.com/mvccore/mvccore/blob/master/src/MvcCore/Application/Dispatching.php#L197).

## Možnosti rozšíření chování
Pokud tedy controller pro svůj běh potřebuje cokoliv navíc od standardního 
chování, může si vývojář vytvořit svoji metodu `__constructor()` v implementaci
konkrétního controlleru a tam si vše navíc inicializovat do properties controlleru.
Nebo je možné podědit statickou metodu [**`\MvcCore\Controller::CreateInstance()`**](https://github.com/mvccore/mvccore/blob/master/src/MvcCore/Controller/Dispatching.php#L26) 
a i zde si implementovat vlastní, klidně i globální chování pro controllery v aplikaci.

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

---

<div class="prev-next">

[předchozí: **Životní cyklus controlleru**](./lifecycle.md)  
[další: **Inicializace controlleru**](./initialization.md)  

</div>