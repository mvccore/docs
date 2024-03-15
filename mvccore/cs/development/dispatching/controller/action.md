# Volání akcí controlleru

Volání metody akce je nejpříhodnější místo pro vyřizování požadavku v controlleru 
podle zachycené routy nebo podle jiného zacílení na určitou akci.

Pro tento případ se volá po přípravě renderování metoda, která v controlleru může a nemusí existovat
a která slouží právě pro vyřizování určité akce, případně její okamžité renderování:
```php
$controller->{$actionNamePascalCase . 'Action'}(): void;
```

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
V této mětodě nevoláme žádnou metodu se stejným názvem ve třídě předka, protože se předpokládá, 
že předek je buď základní controller celé aplikace nebo `\MvcCore\Controller` a pro něj 
nejspíš tato metoda neexistuje nebo nedává smysl.

Pokud má však controller zaregistrované nějaké své sub-controllery 
(pomocí volání `$controller->AddChildController($subController);`), je i na všech sub-controlerech
detekována přítomnost této veřejné metody a pokud metoda existuje, je zavolána 
po volání rodičovské metody.

Je tedy možné routovat tímto způsobem některé akce do sub-controllerů.
TODO: příklad.

Pokud tato metoda akce existuje v hlavním controlleru, ale neexistuje metoda inicializace akce
a ani neexistuje vyhovující view šablona, přesto je controller do této akce routován a vyřizován.
Existence této metody je jedna ze tří možných podmínek, kdy je možné controller vyřizovat, 
pokud je splněna alespoň jedna z nich. Pak nedojde k vyjímce s kódem 404.

## Volání renderování
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

---

<div class="prev-next">

[předchozí: **Příprava renderování controlleru**](./pre-dispatching.md)  
[další: **Renderování a odesílání**](../rendering/README.md)  

</div>