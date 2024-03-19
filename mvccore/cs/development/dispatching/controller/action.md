# Volání akcí controlleru

## Obsah
- [**Úvod**](#úvod)
- [**Příklad**](#příklad)
- [**Bez volání rodičovské metody**](#bez-volání-rodičovské-metody)
- [**Volání v sub-controllerech**](#volání-v-sub-controllerech)
- [**Existence metody jako jedna z podnímek vyřizování controlleru**](#existence-metody-jako-jedna-z-podnímek-vyřizování-controlleru)
- [**Nastavení hodnot šablonám akce a layoutu**](#nastavení-hodnot-šablonám-akce-a-layoutu)
- [**Volání vlastního renderování**](#volání-vlastního-renderování)

## Úvod
Volání metody akce je nejpříhodnější místo pro vyřizování požadavku v controlleru 
podle zachycené routy nebo podle jiného zacílení na určitou akci.

Pro tento případ se volá po přípravě renderování metoda, která v controlleru může a nemusí existovat
a která slouží právě pro vyřizování určité akce, případně její okamžité renderování:
```php
$controller->{$actionNamePascalCase . 'Action'}(): void;
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Příklad
Metoda by měla být vždy veřejná a měla by mít tvar jako název akce v PascalCase + přípona `Action`. 
Například akce `create` bude mít tvar této své vyřizovací metody jako `CreateAction()`:
```php
<?php

namespace App\Controllers;

class Product extends \MvcCore\Controller {

	// url: /product/create
	public function CreateAction () {
	}

	// url: /product/edit/123
	public function EditAction () {
	}
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Bez volání rodičovské metody
V této mětodě nevoláme žádnou metodu se stejným názvem ve třídě předka, protože se předpokládá, 
že předek je buď základní controller celé aplikace nebo `\MvcCore\Controller` a pro něj 
nejspíš tato metoda neexistuje nebo nedává smysl.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Volání v sub-controllerech
Pokud má však controller zaregistrované nějaké své sub-controllery 
(pomocí volání `$controller->AddChildController($subController);`), je i na všech sub-controlerech
detekována přítomnost této veřejné metody a pokud metoda existuje, je zavolána 
po volání rodičovské metody. Více o sub-controllerech najdete v sekci 
[**Vytvoření a renderování sub-controllerů**](../../constructions/sub-controllers-dispatching.md).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Existence metody jako jedna z podnímek vyřizování controlleru
Pokud tato metoda akce existuje v hlavním controlleru, ale neexistuje metoda inicializace akce
a ani neexistuje vyhovující šablona akce, přesto je controller do této akce routován a vyřizován.
Existence této metody je jedna ze tří možných podmínek, kdy je možné controller vyřizovat, 
pokud je splněna alespoň jedna z nich. Pokud není splněna ani jedna, dojde k vyjímce s kódem 404.
Více o podmínkách vyřízení controlleru v sekci [**Průchod zpracování v aplikaci**](../application/app-dispatch.md#vyřízení-životního-cyklu-controlleru).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Nastavení hodnot šablonám akce a layoutu
Akce controlleru je především místo, kdy pro standardní požadavky aplikace existuje view šablona 
(vyjma AJAXu apod.) a kde je třeba šabloně nastavit všechna data. Šablona existuje již od 
volání metody `$controller->PreDispatch(): void;`, kde je možné nastavit opakující se hodnoty šablon.

V akcích ale nastavujeme do šablon vlastnosti (proměnné), specifické pro vyřizovanou akci:
```php
<?php

namespace App\Controllers;

class Product extends \MvcCore\Controller {

	public function IndexAction () {
		// pro jedinou view proměnnou `$productName` nebo `$this->productName`:
		$this->view->productName = 'Vintage Apple Macintosh SE';
		// úrp nastavení více proměnných najednou:
		$this->view->AddData([
			'desc'		=> 'Computer that features 4MB of RAM and a 20MB hard disk drive.'
			'price'		=> 299.95,
			'currency'	=> 'USD',
		]);
	}
}
```

V šabloně poté proměnné používáme buď jako lokální proměnné nebo jako vlastnosti view kontextu `$this`:
```html
<div class="product">
	<h1><?=$productName?></h1>
	<div class="price">Price: <b><?php echo $price; ?></b> <?=$this->currency?></div>
	<p><?php echo $this->desc; ?></p>
</div>
```

Výstup bude vypadat takto:
```html
<div class="product">
	<h1>Vintage Apple Macintosh SE</h1>
	<div class="price">Price: <b>299.95</b> USD</div>
	<p>Computer that features 4MB of RAM and a 20MB hard disk drive.</p>
</div>
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Volání vlastního renderování
V metodě akce je možné i zavolat renderování konkrétní šablony.
Jde o případy, kdy dvě různé akce mají stejnou šablonu:
```php
<?php

namespace App\Controllers;

class Product extends \MvcCore\Controller {

	// url: /product/view/123
	public function ViewAction () {
		// ... something specific for view
		$this->Render('view-edit');
	}

	// url: /product/edit/123
	public function EditAction () {
		// ... something specific for edit
		$this->Render('view-edit');
	}
}
```

Vyrenderuje se tak šablona v umístění `./App/Views/Scripts/product/view-edit.phtml`,
která pro obě akce poslouží stejně a není tak třeba vytvářet dvě identické šablony
nebo do nich includovat nějakou třetí společnou šablonu.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

<div class="prev-next">

[předchozí: **Příprava renderování controlleru**](./pre-dispatching.md)  
[další: **Renderování a odesílání**](../rendering/README.md)  

</div>