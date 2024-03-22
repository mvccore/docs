# Inicializace akcí controlleru

## Obsah
- [**Úvod**](#úvod)
- [**Příklad**](#příklad)
- [**Bez volání rodičovské metody**](#bez-volání-rodičovské-metody)
- [**Volání v sub-controllerech**](#volání-v-sub-controllerech)
- [**Existence metody jako jedna z podnímek vyřizování controlleru**](#existence-metody-jako-jedna-z-podnímek-vyřizování-controlleru)

## Úvod
Často nastává případ, že některá akce controlleru potřebuje inicializaci
provést trochu jiným způsobem, než zbytek akcí v controlleru.

Pro tento případ se volá po inicializaci další metoda, která v controlleru může a nemusí existovat
a která slouží právě pro specifickou inicializaci určité akce:
```php
$controller->{$actionNamePascalCase . 'Init'}(): void;
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Příklad
Metoda by měla být vždy veřejná a měla by mít tvar jako název akce v PascalCase + přípona `Init`. 
Například akce `create` bude mít tvar této své specifické inicializace jako název metody `CreateInit()`:
```php
<?php

namespace App\Controllers;

class Product extends \MvcCore\Controller {
	
	protected ?\App\Models\Product $product = NULL;

	// url: /product/create
	public function CreateInit () {
		$this->product = new \App\Models\Product;
	}

	// url: /product/view/123
	public function ViewInit () {
		$this->initProduct();
	}

	// url: /product/edit/123
	public function EditInit () {
		$this->initProduct();
	}

	protected initProduct (): void {
		$id = $this->GetParam('id', '0-9', NULL, 'int');
		if ($id !== NULL) 
			$this->product = \App\Models\Product::GetById($id);
		if ($this->product === NULL)
			throw new \MvcCore\Controller\TerminateException('Product not found.', 404);
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
Pokud tato inicializační metoda existuje v hlavním controlleru, ale neexistuje metoda akce
a ani neexistuje vyhovující šablona akce, přesto je controller do této akce routován a vyřizován.
Existence této metody je jedna ze tří možných podmínek, kdy je možné controller vyřizovat, 
pokud je splněna alespoň jedna z nich. Pokud není splněna ani jedna, dojde k vyjímce s kódem 404.
Více o podmínkách vyřízení controlleru v sekci [**Průchod zpracování v aplikaci**](../application/app-dispatch.md#vyřízení-životního-cyklu-controlleru).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

[▲ o úroveň výš](../README.md)

<div class="prev-next">

[◀ předchozí: **Inicializace controlleru**](./initialization.md)  
[▶ další: **Příprava renderování controlleru**](./pre-dispatching.md)  

</div>