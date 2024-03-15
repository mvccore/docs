# Inicializace akcí controlleru

Často nastává případ, že některá akce controlleru potřebuje inicializaci
provést trochu jiným způsobem, než zbytek akcí v controlleru.

Pro tento případ se volá po inicializaci další metoda, která v controlleru může a nemusí existovat
a která slouží právě pro specifickou inicializaci určité akce:
```php
$controller->{$actionNamePascalCase . 'Init'}(): void;
```

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

V této mětodě nevoláme žádnou metodu se stejným názvem ve třídě předka, protože se předpokládá, 
že předek je buď základní controller celé aplikace nebo `\MvcCore\Controller` a pro něj 
nejspíš tato metoda neexistuje nebo nedává smysl.

Pokud má však controller zaregistrované nějaké své sub-controllery 
(pomocí volání `$controller->AddChildController($subController);`), je i na všech sub-controlerech
detekována přítomnost této veřejné metody a pokud metoda existuje, je zavolána 
po volání rodičovské metody.

Je tedy možné routovat tímto způsobem inicializaci některé akce do sub-controllerů.
TODO: příklad.

Pokud tato inicializační metoda existuje v hlavním controlleru, ale neexistuje metoda akce
a ani neexistuje vyhovující view šablona, přesto je controller do této akce routován a vyřizován.
Existence této metody je jedna ze tří možných podmínek, kdy je možné controller vyřizovat, 
pokud je splněna alespoň jedna z nich. Pak nedojde k vyjímce s kódem 404.

---

<div class="prev-next">

[předchozí: **Inicializace controlleru**](./initialization.md)  
[další: **Příprava renderování controlleru**](./pre-dispatching.md)  

</div>