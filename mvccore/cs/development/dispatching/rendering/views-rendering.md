# Renderování šablon

## Obsah
- [**Úvod**](#úvod)
- [**Šablona akce**](#šablona-akce)
- [**Umístění šablony akce**](#umístění-šablony-akce)
- [**Existence šablony akce jako jedna z podnímek vyřizování controlleru**](#existence-šablony-akce-jako-jedna-z-podnímek-vyřizování-controlleru)
- [**Šablona layoutu**](#šablona-layoutu)
- [**Umístění šablony layoutu**](#umístění-šablony-layoutu)
- [**Vykreslení obsahu šablony akce uvnitř šablony layoutu**](#vykreslení-obsahu-šablony-akce-uvnitř-šablony-layoutu)

## Úvod
Renderování šablon v MvcCore funguje na jednoduchém nativním principu PHP renderování:
```php
// obsah šablony: ./cesta/index.phtml
<h1><?php echo 'Hello world!'; ?></h1>

// ilustrativní princip renderování:
ob_start();
include('./cesta/index.phtml');
$result = ob_get_clean();

// obsah proměnné $result:
'<h1>Hello world!</h1>'
```
Renderování je tedy nad míru rychlé a v syntaxy, kterou každý zná.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Šablona akce

Vykreslovací šablona akce se používá na specifické části odpovědi aplikace.
V HTML odpovědi např. pro obsahový element někde v `<body>` dokumentu.
Pro XML RSS odpověď např. cyklus s elementy `<item>` pro tag `<channel>`.

Šablona akce musí ležet v jediném konkrétním umístění, které je odvozeno 
podle celého názvu třídy controlleru a podle názvu akce. Jediným fixním umístěním
se renderování urychluje, protože není třeba prohledávat pro každou 
šablonu několik volitelných míst.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Umístění šablony akce
Uvažujme následující controller a akci:
- PHP název třídy a metody akce:  
  `\App\Controllers\EshopOrder\Chart::ItemsAction()`,
- příp. stejný zápis v routě:  
  `EshopOrder\Chart::Items`,
- příp. stejný zápis v query stringu:  
  `?controller=eshop-order/chart&action=items`.

Šablona musí ležet v umístění `./App/Views/Scripts/eshop-order/chart/items.phtml`.

Cestu lze odvodit vždy velmi jednoduše a skládá se z těchto částí:
```
controller:akce       \App\Controllers       \EshopOrder\Chart      :  Items           Action()
cesta šablony         ./App/Views/Scripts    /eshop-order/chart     /  items           .phtml
vysvětlení cesty      [ cesta šablon akcí ]  [ cesta controlleru ]  /  [ název akce ]  .[ přípona ]
```
- cesta šablon akcí: `./App/Views/Scripts`
- cesta controlleru: `/eshop-order/chart`
  - odvodí se podle specifické části namespace controlleru
  - je nutné odebrat základní prefix `\App\Controllers`, zbyde pouze: `\EshopOrder\Chart`
  - specifická část se převede na dashed-case a dopředná lomítka: `/eshop-order/chart`
- lomítko: `/`
- název akce: `items`
  - je nutné odebrat příponu `Action`, zbyde pouze: `Items`
  - zbytek názvu akce se převede na dashed-case: `items`
- přípona vykreslovacích šablon: `.phtml`
  - `.phtml` je výchozí konfigurovatelná hodnota pro přípony šablon

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Existence šablony akce jako jedna z podnímek vyřizování controlleru
Pokud šablona akce existuje ve svém umístění, ale neexistuje v controlleru metoda inicializace akce
a ani neexistuje v controlleru metoda akce, přesto je controller do routované akce vyřizován a dojde 
k vykreslení šablon. Existence šablony akce je jedna ze tří možných podmínek, kdy je možné controller 
vyřizovat, pokud je splněna alespoň jedna z nich. Pokud není splněna ani jedna, dojde k vyjímce s kódem 404.
Více o podmínkách vyřízení controlleru v sekci [**Průchod zpracování v aplikaci**](../application/app-dispatch.md#vyřízení-životního-cyklu-controlleru).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Šablona layoutu
Vykreslovací šablona layoutu se používá na opakující se části odpovědi aplikace
okolo šablony akce. V HTML odpovědi jde např. o celý začátek odpovědi, hlavičku `<head>`
a počátek elementu `<body>` až k obsahovému elementu stránky. Pak od konce obsahového 
elementu až na konec odpovědi.
Pro XML RSS odpověď se jedná např. o část od hlavičky XML až po první tak `<item>`
a pak uzavření tagu `</channel>` až na konec odpovědi:

Příklad HTML šablony layoutu:
```php
<!DOCTYPE HTML>
<html lang="<?=$localization?>">
	<head>
		<meta charset="UTF-8" />
		<title><?=$title?></title>
	</head>
	<body>
		<?=$this->GetContent()?>
	</body>
</html>
```

Příklad XML šablony layoutu:
```xml
<?xml version="1.0" encoding="utf-8"?>
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
	<channel>
		<title><?=$title?></title>
		<description><?=$description?></description>
		<link><?=$request->GetBaseUrl()?></link>
		<?=$this->GetContent()?>
	</channel>
</rss>
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Umístění šablony layoutu
Šablona layoutu musí také ležet v jediném konkrétním umístění, které je 
pevně dáno nastavením protected vlastnosti controlleru `$controller->layout`
a nastavením složky se všemi layoutovými šablonami.

Výchozím layoutem je hodnota `layout`, proto šablona layoutu musí ležet 
v umístění `./App/Views/Layouts/layout.phtml`.

Cestu lze odvodit vždy velmi jednoduše a skládá se z těchto částí:
```
cesta šablony         ./App/Views/Layouts       /  layout             .phtml
vysvětlení cesty      [ cesta šablon layoutů ]  /  [ cesta layoutu ]  .[ přípona ]
```
- cesta šablon layoutů: `./App/Views/Layouts`
- lomítko: `/`
- cesta layoutu: `layout`
  - může obsahovat i lomítko a názvy podsložek
- přípona vykreslovacích šablon: `.phtml`
  - `.phtml` je výchozí konfigurovatelná hodnota i pro přípony šablon layoutů

Předpokládá se, že tuto vlastnost bude vývojář redefinovat na různých 
úrovních hierarchie dědičnosti svých controllerů podle toho, čemu se 
controllery souhrnně věnují podle své dědičnosti.

```php
// ./App/Controllers/Front.php
namespace App\Controllers;

class Front extends \MvcCore\Controller {
	protected $layout = 'front'; // použije se layout šablona ./Views/Layouts/front.phtml
}

// ./App/Controllers/Admin.php
namespace App\Controllers;

class Admin extends \MvcCore\Controller {
	protected $layout = 'admins/standard'; // použije se layout šablona ./Views/Layouts/admins/standard.phtml
}

// ./App/Controllers/Admins/Invoice.php
namespace App\Controllers\Admins;

class Invoice extends App\Controllers\Admin {
	protected $layout = 'admins/print'; // použije se layout šablona ./Views/Layouts/admins/print.phtml
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Vykreslení obsahu šablony akce uvnitř šablony layoutu
V layoutu potřebujeme vždy vykreslit i specifický obsah šablony akce. K tomu slouží následující volání:
```php
<?php echo $this->GetContent(); ?>
```
Nelze používat zkrácený zápis lokální proměnné `$content` takto: <s>`<?=$content?>`</s>. Vždy musí jít o volání metody `<?=$this->GetContent()?>` v kontextu šablony view.

Volání používáme pro oba módy renderování:
- [**Mód renderování přes output buffering**](./rendering-modes.md#renderování-přes-output-buffering-do-mvccoreresponse):
  - vypíše již vyrenderovanou šablonu akce jako `string`,
- [**Mód přímého renderování na výstup**](./rendering-modes.md#přímé-renderování-na-výstup):
  - započne, provede a ukončí renderování šablony akce.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

[▲ o úroveň výš](../README.md)

<div class="prev-next">

[◀ předchozí: **Módy renderování**](./rendering-modes.md)  
[▶ další: **Proměnné šablon**](./view-variables.md)  

</div>