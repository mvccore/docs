# Průchod zpracování v aplikaci

## Obsah
- [**Úvod**](#úvod)
- [**Inicializace/ověření existence hlavních objektů**](#inicializaceověření-existence-hlavních-objektů)
- [**Vyřízení požadavku**](#vyřízení-požadavku)
  - [**Nalezení routy požadavku**](#nalezení-routy-požadavku)
  - [**Vytvoření instance controlleru**](#vytvoření-instance-controlleru)
  - [**Vyřízení životního cyklu controlleru**](#vyřízení-životního-cyklu-controlleru)
- [**Vyřízení případné vyjímky**](#vyřízení-případné-vyjímky)
- [**Ukončení požadavku a odeslání odpovědi**](#ukončení-požadavku-a-odeslání-odpovědi)

## Úvod

Pro detailní přehled o zpracování požadavku v aplikaci doporučuji přečíst  
si PHP trait sloužící pro vyřizování požadavku v aplikaci (cca 500 řádků):
[**`./vendor/mvccore/mvccore/src/MvcCore/Application/Dispatching.php`**](https://github.com/mvccore/mvccore/blob/master/src/MvcCore/Application/Dispatching.php)

Protože MvcCore framework se snaží co nejvíce zachovávat zpětnou kompatibilitu,
je v metodě `$app->Dispatch()` obaleno vyřizování do dvojí vyjímky záměrně.

Všechny metody třídy aplikace lze libovolně rozšiřovat a upravovat jejich chování 
podle svých potřeb.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Životní cyklus požadavku v aplikaci

Vyřizování požadavku uvnitř instance aplikace lze rozdělit do těchto kroků:
- Inicializace/ověření existence hlavních objektů
- Vyřízení požadavku
  - Nalezení routy požadavku
  - Vytvoření instance controlleru
  - Vyřízení životního cyklu controlleru
- Vyřízení případné vyjímky
- Ukončení požadavku a odeslání odpovědi

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Inicializace/ověření existence hlavních objektů
Aplikace pro vyřízení požadavku potřebuje provést 4 základní inicializace:

- Vytvoření objektu prostředí a jeho detekce:
  - `$app->GetEnvironment()->GetName()`
- Vytvoření objektu dotazu:
  - `$app->GetRequest()`
- Vytvoření objektu odpovědi:
  - `$app->GetResponse()`
- Inicializace debuggingu a logování:
  - `$debugClass:Init()`

To vše je k nalezení v metodě `\MvcCore\Application::DispatchInit()`.

Pokud již některé z výše zmíněných volání proběhlo již v `Bootstrap.php`,
nevadí a znovu se již neprovede. Tato funkce jen zajišťuje, že je vše připraveno.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Vyřízení požadavku
Vyřizování požadavku je v metodě `\MvcCore\Application::DispatchExec()`.

Zde je nalezení routy požadavku, vytvoření controlleru a vyřízení jeho životního 
cyklu prokládáno volánimi vlastních aplikačních handlerů. Tyto handlery jsou 
obvykle definovány v `Bootstrap.php` nebo i za běhu aplikace a zde poté dochází 
k jejich volání.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Nalezení routy požadavku
Routa požadavku se hledá v metodě `\MvcCore\Application::RouteRequest()`.

Zde je volání funkce `$app->GetRouter()`, které ověří, zda existuje v
objektu aplikace router (který již může existovat z Bootstrapu),
a do objektu routeru se nastaví aktuální request a zavolá se funkce `$router->Route();`.
Router tímto vyhodnotí dotaz a vrátí některou zachycenou nakonfigurovanou 
routu nebo vytvoří defaultní routu pro dotaz pomocí query stringu.

```php
$router = $this->GetRouter()->SetRequest($this->request);
return $router->Route();
```

Aplikace v MvcCore podporují 2 možnosti routování. Buď pomocí hezkých URL adres
a definovaných rout nebo pomocí routování s parametry v query stringu.
Více o routování je k nalezení v sekci [**Routování dotazů**](./request-routing.md).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Vytvoření instance controlleru

Příprava dat pro vytváření controlleru a další vyřizování požadavku je v metodě:
- `\MvcCore\Application::SetUpController();`,

instance controlleru je poté vytvářena na konci této metody voláním:
- `\MvcCore\Application::CreateController();`.

Metoda `$app->SetUpController()` z aktuálně zachycené routy dostane buď plný nebo relativní název 
třídy controlleru a název akce. Obojí v PascalCase syntaxy. Kontroluje se zde 
pouze existence souboru třídy controlleru, nikoliv existence jeho akcí, to až později.

Je zde provedeno i sestavení cesty pro cílovou šablonu akce podle 
controlleru a akce. Nedochází zde ještě ke kontrole existence souboru šablony,
to se také provede až později.

Na základe plného názvu třídy controlleru se na konci metody `$app->SetUpController()` vytvoří 
nová instance controlleru pomocí volání:
```php
$app->CreateController(
	$controllerName, $actionPc, $viewScriptFullPath
);
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Vyřízení životního cyklu controlleru

Metoda `$app->CreateController()` vytvoří instanci controlleru vždy voláním:
```php
$controller = $ctrlClassFullName::CreateInstance();
```

Třída controlleru by měla mít prázdný konstruktor, nebo by měla rozšiřovat 
statickou metodu `\MvcCore\Controller::CreateInstance()`, ve které si 
vyřeší svoje případné speciální instancování, pokud je to opravdu nutné.

Po vytvoření instance se instance controlleru naplní potřebnými aplikačními objekty
(pomocí veřejných setterů controlleru), které bude controller potřebovat pro svůj životní cyklus:
- `\MvcCore\Application`,
- `\MvcCore\Environment`,
- `\MvcCore\Request`,
- `\MvcCore\Response`,
- `\MvcCore\Router`.

Poté v metodě `$app->CreateController()` dochází k ověření, zda platí 
alespoň jedno z následujících:
- controller má veřejnou metodu `$controller->{$actionName . 'Init'}()`,
- controller má veřejnou metodu `$controller->{$actionName . 'Action'}()`,
- existuje soubor šablony podle názvu controlleru a akce.

Pokud platí alespoň jedno z následujících, controller je dále vyřizován.

Pokud ne, dochází zde k vyhození vyjímky s kódem 404 a následně 
je automaticky zpracováván chybový stav aplikace pro nenalezenou stránku.

Více o konkrétních voláních v rámci životního cyklu controlleru uvnitř controlleru
je možné najít v sekci [**Životní cyklus Controlleru**](../controller/lifecycle.md).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Vyřízení případné vyjímky

Pokud dojde k jakékoliv neošetřené vyjímce v rámci vyřizování požadavku uvnitř aplikace,
tedy k vyjímce zachycené v bloku `try/catch` v metodě `$app->Dispatch()`, je automaticky
vyřizován chybový stav aplikace pomocí dvou možných cest:

1. Vytvoření a vyřízení výchozího controlleru a chybové akce,
2. Vyřízení pouze textové odpovědi.

Protože je možné, že dochází k vyjímce v některé základní úrovni aplikace,
která může mít vliv i na běh výchozího controlleru a chybové akce, je vždy nejprve 
vyzkoušena první cesta pomocí výchozího controlleru a chybové akce a pokud toto 
vyřizování také selže, je odeslán jen textový výstup.

Chyba je logována automaticky vyjma vývojového prostředí, kdy je zobrazena chyba přímo vývojáři.

Vyřizení popsaného chybového stavu dochází v metodě `$app->DispatchException()`.

Z této metody jsou volány dvě různé cesty vyřízení chybového požadavku
(pokud není aktuální prostředí `dev`, kdy jsou chyby rovnou zobrazovány vývojáři):
- `$app->RenderNotFound()` - pro vyjímky s kódem 404,
- `$app->RenderError()` - pro všechny ostatní vyjímky.

V obou těchto metodách je nejprve vytvořen výchozí controller a na něm poté vyřízena 
chybová akce, která vede k odeslání obecné chybové stránky s textem vyjímky nebo 
s vlastním textem ke klientovi.

Pokud vyřizování chybového controlleru jakkoliv selže, je odeslána pouze textová 
odpověď s textem vyjímky a HTTP chybovým kódem podle kódu vyjímky.

Pokud není implementována ani jedna error akce ve výchozím controlleru, 
je automaticky odeslána pouze textová odpověď s obecným hlášením v produkčním módu.

Příklad implementace výchozí akce a chybových akcí výchozího controlleru:
```php
<?php // ./App/Controllers/Index.php

namespace App\Controllers;

class Index extends \MvcCore\Controller {

	public function IndexAction (): void {
	}
	
	public function NotFoundAction (): void {
		$this->ErrorAction();
	}

	public function ErrorAction (): void {
		$code = $this->response->GetCode();
		$message = $this->request->GetParam('message', FALSE);
		$this->view->title = "Error {$code}";
		$this->view->message = $message; // be carefull, what you are telling to user!
		$this->Render('error');
	}
}
```
```php
// ./App/Views/Scripts/index/error.phtml
<h1><?=$title?></h1>
<p><?=$escape($message)?></p>
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Ukončení požadavku a odeslání odpovědi

Ukončení vyřizování dotazu aplikace interně nepoužívá a nelze provádět pomocí 
metod `die();`, `exit();` nebo `exit;`, protože tak nedochází k případnému 
ukládání sezení nebo k volání dalších nakonfigurováných handlerů aplikace.

Ukončení aplikace je voláno automaticky a prováděno v metodě `$app->Terminate()`.

Zde se postupně provádějí tyto volání:
- registrace shutdown handleru pro uložení sezení (pokud je nějaké nastartováno),
- odeslání HTTP hlaviček a těla odpovědi (pokud ještě nebylo učiněno),
- ukončení všech controllerů,
- volání vlastních handlerů aplikace po ukončení vyřizování požadavku.

Po ukončení aplikace je třeba počítat i s voláním registorvaných handlerů pomocí 
PHP funkce `register_shutdown_function()`. Toto volání používá interně 
MvcCore k ukládání sezení, aby tak bylo možné i po vyřízení požadavku aplikace 
ve vlastním handleru (definovaném jako `$app->AddPostTerminateHandler(...);`
sahat aktivně na hodnoty v sezení a případně změnit co je třeba.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

[▲ o úroveň výš](../README.md)

<div class="prev-next">

[◀ předchozí: **Start aplikace**](./app-start.md)  
[▶ další: **Routování dotazů**](./request-routing.md)  

</div>