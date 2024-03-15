# Příprava renderování controlleru

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

Controller má automaticky rozšířením třídy `\MvcCore\Controller` tuto metodu implementovanou od předka.  
Pokud chceme něco k této mětodě přidat, je nutné vždy volat stejně pojmenovanou metodu rodičovské třídy.
Rodičovskou metodu bychom měli volat ideálně jako první statement v metodě `PreDispatch()`.

Obvykle slouží metoda `PreDispatch()` v základních controllerech k tomu, abychom 
do view šablony naincializovali všechny proměnné používané v layout šablonách nebo 
používané opakovaně ve většině šablon akcí:
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

Zavoláním rodičovské metody `parent::PreDispatch();` zařídíme vytvoření view za zmíněných podmínek.
Pokud chceme vytváření objektu view také upravit, je třeba podědit a rozšířit metodu 
`\MvcCore\Controller::createView(bool $actionView = TRUE): \MvcCore\View;`:
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

---

<div class="prev-next">

[předchozí: **Inicializace akcí controlleru**](./action-initialization.md)  
[další: **Volání akcí controlleru**](./action.md)  

</div>