# Příprava renderování controlleru

## Obsah
- [**Úvod**](#obsah)
- [**Příklad**](#příklad)
- [**Volání rodičivské metody**](#volání-rodičivské-metody)
- [**Vlastní vytvoření view objektu šablony**](#vlastní-vytvoření-view-objektu-šablony)

## Úvod
Příprava renderování je jednoduchý proces, kdy vzniká instance vykreslovací šablony `$controller->view`.

Vykreslovací šablona v této metodě nevzniká pro dotazy volané AJAXem, kdy je hodnota `$controller->ajax` 
nastavená na `TRUE`. Pro AJAXové dotazy je taktéž automaticky vlastnost controlleru `$controller->viewEnabled` 
nastavená na `FALSE`. Vytváření view může vypnout i vývojář z jakéhokoliv jiného důvodu pomocí 
`$controller->SetViewEnabled(FALSE);` a tím také zamezit vytváření view objektu v přípravě renderování.

Aplikace volá přípravu renderování veřejnou metodou:
```php
$controller->PreDispatch(): void;
```

Název této metody je odvozen z toho, že dispatching controlleru je především vždy volání 
nějaké akce controlleru + její následné vyrenderování. Příprava před touto hlavní 
a nejobvyklejší činností controlleru se tak nazývá `PreDispatch()`.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Příklad
Tato metoda je volána v controlleru pro všechny definované akce. Například pokud má controller následující
akce, metoda `PreDispatch()` se zavolá před každou z nich, ať už se dotazuji na jednu nebo druhou URL.
Jde o metodu, která řeší inicializaci objektů pro všechny společné věci všech akcí v controlleru v okamžiku, 
kdy již existuje object `$controller->view`, který v inicializačních metodách neexistuje:
```php
<?php

namespace App\Controllers;

class Product extends \MvcCore\Controller {
    
    protected ?\App\Models\Product $product = NULL;

    public function Init (): void {
        parent::Init();
        $id = $this->GetParam('id', '0-9', NULL, 'int');
        if ($id !== NULL) 
            $this->product = \App\Models\Product::GetById($id);
        if ($this->product === NULL)
            throw new \MvcCore\Controller\TerminateException('Product not found.', 404);
    } 

    public PreDispatch (): void {
        parent::PreDispatch();
        $this->view->product = $this->product;
    }

    // url: /product/view/123
    public function ViewAction () {
        // ...
    }

    // url: /product/edit/123
    public function EditAction () {
        // ...
    }

}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Volání rodičivské metody
Controller má automaticky rozšířením třídy `\MvcCore\Controller` tuto metodu implementovanou od předka.  
Pokud chceme něco k této mětodě přidat, je nutné vždy volat stejně pojmenovanou metodu rodičovské třídy.
Rodičovskou metodu bychom měli volat ideálně jako první statement v metodě `PreDispatch()`.

Obvykle slouží metoda `PreDispatch()` v základních controllerech k tomu, abychom 
do již inicializované view šablony nastavili všechny proměnné používané opakovaně 
v šablonách akcí a v layout šablonách:
```php
<?php

namespace App\Controllers;

class Base extends \MvcCore\Controller {
    
    public function PreDispatch (): void {
        parent::PreDispatch();
        if ($this->viewEnabled) {
            $this->view->user = $this->user;
            $this->view->basePath = $this->request->GetBasePath();
        }
    }
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Vlastní vytvoření view objektu šablony
Zavoláním rodičovské metody `parent::PreDispatch();` zařídíme vytvoření view za zmíněných podmínek.
Pokud chceme vytváření objektu view také upravit, je třeba podědit a rozšířit metodu 
`\MvcCore\Controller::createView(bool $actionView = TRUE): \MvcCore\View;`.
Její první parametr s hodnotou `TRUE` vyjadřuje, zda se vytváří view objekt pro šablonu akce.
Pokud má hodnotu `FALSE`, vytváří se view objekt pro šablonu layoutu.
Více o šablonách akcí a layoutu v sekci [**Renderování šablon**](../rendering/views-rendering.md).
```php
<?php

namespace App\Controllers;

class Base extends \MvcCore\Controller {
    
    protected function createView ($actionView = TRUE): \MvcCore\View {
        $view = parent::createView($actionView);
        // ... do anything special with $view
        return $view;
    }
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

<div class="prev-next">

[předchozí: **Inicializace akcí controlleru**](./action-initialization.md)  
[další: **Volání akcí controlleru**](./action.md)  

</div>