# Inicializace controlleru

## Obsah
- [**Úvod**](#úvod)
- [**Kdy je inicializace volána, příklad**](#kdy-je-inicializace-volána-příklad)
- [**Volání rodičivské metody**](#volání-rodičivské-metody)
- [**Interní zaregistrování `$this` do kolekce všech controllerů**](#interní-zaregistrování-this-do-kolekce-všech-controllerů)
- [**Automatické nastartování session podle `$controller->autoStartSession`**](#automatické-nastartování-session-podle-controller-autostartsession)
- [**Automatická inicializace označených vlastností**](#automatická-inicializace-označených-vlastností)
  - [**Upozornění pro auto-inicializaci**](#upozornění-pro-auto-inicializaci)
  - [**Zapnutí auto-inicializace**](#zapnutí-auto-inicializace)
  - [**Co lze automaticky inicializovat**](#co-lze-automaticky-inicializovat)
  - [**Tovární metody auto-inicializace**](#tovární-metody-auto-inicializace)
  - [**Více továrních metod a pořadí**](#více-továrních-metod-a-pořadí)
  - [**Automatická inicializace versus Dependency Injection pattern**](#automatická-inicializace-versus-dependency-injection-pattern)
- [**Provolání `Init()` metody na všech dětech**](#provolání-init-metody-na-všech-dětech)

## Úvod
Inicializace controlleru samozřejmě může probíhat i ve vlastním konstruktoru controlleru.  
Zde je však "inicializace controlleru" nazýván proces, kdy je na nastavené instanci controlleru 
volána aplikací veřejná metoda:
```php
$controller->Init(): void;
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Kdy je inicializace volána, příklad
Tato metoda je volána v controlleru pro všechny definované akce. Například pokud má controller následující
akce, metoda `Init()` se zavolá před každou z nich, ať už se dotazuji na jednu nebo druhou URL.
Jde o metodu, která řeší inicializaci objektů pro všechny společné věci všech akcí v controlleru:
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
Pokud chceme něco k této metodě přidat, je nutné vždy volat stejně pojmenovanou metodu rodičovské třídy.
Rodičovskou metodu bychom měli volat ideálně jako první statement v metodě `Init()`:
```php
<?php

namespace App\Controllers;

class Base extends \MvcCore\Controller {
    
    public function Init (): void {
        parent::Init();
        
        // něco navíc v úvodu aplikace:
        date_default_timezone_set('Europe/Paris');
        setlocale(LC_ALL, 'fr_FR.UTF-8');
    } 
}
```

Zavoláním rodičovské metody `parent::Init();` zařídíme:
- interní zaregistrování `$this` do kolekce všech kontrollerů, pokud ještě neproběhlo,
- automatické nastartování session podle `$controller->autoStartSession`,
- automatické nastavení typu odpovědi aplikace (hlavička `Content-Type`) 
  na výchozí hodnotu `text/html`, pokud je proměnná `$this->ajax` 
  s hodnotou `TRUE`, nastaví se typ odpovědi na `text/javascript`,
- automatická inicializace všeh označených vlastností - viz. níže,
- provolání `Init()` metody na všech dětech (sub-controllerech) 
  aktuálního controlleru.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Interní zaregistrování `$this` do kolekce všech controllerů
Každý controller je při svém vytvoření v metodě `\MvcCore\Controller::CreateInstance();` zaregistrován 
do skladu všech controllerů, aby bylo možné s controllery globálně pracovat frameworkem.
Pokud je metoda `\MvcCore\Controller::CreateInstance();` implementována zcela jinak, 
volání rodičovské funkce `parent::Init()` pro jistotu ověří, zda je aktuální kontext controlleru 
`$this` zaregistrován v tomto skladu všech controllerů a případně registraci provede.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Automatické nastartování session podle `$controller->autoStartSession`
Pokud je z nějakého důvodu třeba nastartovat session pro každý požadavek na aplikaci,
slouží k tomuto účelu nastavení protected vlastnosti na `$controller->autoStartSession = TRUE;`.
Výchozím stavem je hodnota `FALSE`, protože session se startuje vždy až když je skutečně třeba.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Automatická inicializace označených vlastností
Další objekty v kontextu controlleru (jako jsou formuláře, datagridy nebo jakékoliv 
další controllery vystupující jako děti hlavního vyřizovaného controlleru) je možné
automaticky inicializovat při volání metody `parent::Init()` a to tak, že dekorujeme 
tyto lokální vlastnosti controlleru pomocí PHPDocs tagu `@autoInit` nebo pomocí 
PHP 8 atributu `#[\MvcCore\Controller\AutoInit]`:
```php
<?php

namespace App\Controllers;

use \MvcCore\Controller\AutoInit;

class Base extends \MvcCore\Controller {
    
    #[AutoInit]
    protected \App\Forms\Register $registerForm;

    public function Init (): void {
        parent::Init(); // automatická inicializace $registerForm
        // ...
    } 
}
```

Starší zápis pomocí PHPDocs vypadá takto:
```php
    /**
     * @autoInit
     * @var \App\Forms\UserRegistration
     */
    protected $registerForm;
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Upozornění pro auto-inicializaci
Tato funkcionalita je magie pro nové vývojáře a není na první pohled jasná.
Prosím buďte opatrní, zda křivku učení pro své kolegy budete dělat složitější.

Sub-controllery nebo jakékoliv objekty je možné řešit i více jasněji a stojí to menší procesní čas.
Proces vyřizování životního cyklu controlleru a sub-controllerů je v tomto velmi flexibilní,
proto je klidně možné sub-controller vytvořit i později (až když je třeba) a vše bude stále OK.

Proto tuto funkcionalitu používejte opravdu jen tehdy, pokud jste velmi líní a procesní čas Vás netrápí.

Auto-inicializace navíc probíhá pro všechny akce daného controlleru. Pokud automaticky inicializujete
nějaký sub-controller v předkovi všech controllerů, je tato auto-inicializace prováděna
opravdu pro všechny dotazy na aplikaci, pokud to explicitně neopodmínkujete v tovární metodě.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Zapnutí auto-inicializace

Tímto označením jakékoliv vlastnosti s jakýmkoliv modifikátorem přístupu můžeme automaticky
inicializovat dané vlastnosti do jejich definovaných typů. Tato funkcionalita se však neděje vždy,
protože stojí nějaký procesní čas a proto je pro zapnutí této magie třeba nastavit v aktuálním 
kontextu controlleru vlastnost `$controller->autoInitProperties = TRUE`, která má jinak výchozí hodnotu `FALSE`.

Detailní podoba těchto automatických inicializací je v `\MvcCore\Controller::autoInitializeProperties();`.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Co lze automaticky inicializovat

Je možné takto instancovat automaticky objekty, které mají veřejnou statickou metodu `ClassName::CreateInstance();`
vracející svoji novou instanci nebo které mají jednoduše prázdný konstruktor nebo konstruktor, 
do kterého není nutné poslat žádný parametr. 

Pokud některá automaticky inicializovaná vlastnost implementuje rozhraní `\MvcCore\IController`,
je instance této inicializované vlastnosti automaticky zaregistrivána jako sub-controller - tedy jako dítě aktuálního 
kontextu controlleru a takový sub-controller je pak automaticky vyřizován ve standardním životním cyklu frameworku.
Registrace sub-controlleru se interně provádí pomocí `$controller->AddChildController($subController);` automaticky.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Tovární metody auto-inicializace
Pokud potřebujeme vyrobit instanci ale předat ji nějaké parametry konstruktoru nebo 
ji po inicializaci vždy nějak nastavit, můžeme využít toho, že proces automatické inicializace 
vlastností controlleru nejprve hledá tovární metody v kontextu controlleru a pokud existují,
zavolá je a neinstancuje automaticky. Předpokládá se, že tovární metoda vrátí vytvořenou a správně 
nastavenou instanci a neakceptuje žádný parametr:
```php
<?php

namespace App\Controllers;

use \MvcCore\Controller\AutoInit;

class Base extends \MvcCore\Controller {
    
    #[AutoInit]
    protected \App\Forms\Register $registerForm;

    // tovární metoda
    protected function createRegisterForm (): \App\Forms\Register {
        $form = new \App\Forms\Register($this);
        $form->SetAction($this->Url(':Submit'));
        return $form;
    }

    public function Init (): void {
        parent::Init(); // automatická inicializace $registerForm
        // ...
    } 
}
```

Automatická inicializace hledá metody vždy ve tvaru `create` nebo `_create` plus
název vlastnosti s prvním velkým písmenem (předpokládá se název vlastnosti převážně v camelCase).
Příklad pro vlastnost `$registerForm` => `$controller->createRegisterForm()` nebo `$controller->_createRegisterForm()`.

Pokud chceme vlastní názvy továrních metod, je možné využít první parametr PHP atributu `AutoInit`,
který slouží pro definici názvu tovární metody pro automatickou inicializaci dané vlastnosti:
```php
<?php

namespace App\Controllers;

use \MvcCore\Controller\AutoInit;

class Base extends \MvcCore\Controller {
    
    #[AutoInit('getRegisterForm')]
    protected \App\Forms\Register $registerForm;

    // vlastní název pro tovární metodu
    protected function getRegisterForm (): \App\Forms\Register {
        $form = new \App\Forms\Register($this);
        $form->SetAction($this->Url(':Submit'));
        return $form;
    }

    public function Init (): void {
        parent::Init(); // automatická inicializace $registerForm
        // ...
    } 
}
```
Starší zápis pak vypadá takto:
```php
    /**
     * @autoInit getRegisterForm
     * @var \App\Forms\UserRegistration
     */
    protected $registerForm;
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Více továrních metod a pořadí
Pokud má controller více vlastností, které je třeba automaticky inicializovat a je mezi nimi nějaká provázanost,
potřebujeme vyřešit, která tovární metoda se zavolá dříve, aby další mohla počítat již s existencí nějakého objektu.
Pro tyto případy má PHP atribut `AutoInit` druhý parametr a to je celé číslo pro případné pořadí.

Pokud uvedene nějaké pořadí, prioritizujeme automaticky instancování dané vlastnoti před všechny ostatní, 
které žádné pořadí nemají:
```php
<?php

namespace App\Controllers;

use \MvcCore\Controller\AutoInit;

class Base extends \MvcCore\Controller {
    
    #[AutoInit('getRegisterForm', 2)]
    protected \App\Forms\Register $registerForm;

    #[AutoInit('getGrid')]
    protected \App\Controllers\DataGrid $grid;

    protected function getRegisterForm (): \App\Forms\Register {
        $form = new \App\Forms\Register($this);
        $form->SetAction($this->Url(':Submit'));
        $form->SetGrid($this->grid);
        return $form;
    }

    protected function getGrid (): \App\Models\Store {
        $grid = new \App\Controllers\DataGrid($this);
        $grid->SetModelClass(\App\Models\User::class);
        return $grid;
    }

    public function Init (): void {
        parent::Init(); // automatická inicializace $registerForm
        // ...
    } 
}
```
Starší zápis pak vypadá takto:
```php
    /**
     * @autoInit getRegisterForm, 2
     * @var \App\Forms\Register
     */
    protected $registerForm;

    /**
     * @autoInit getGrid, 1
     * @var \App\Controllers\DataGrid
     */
    protected $grid;
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Automatická inicializace versus Dependency Injection pattern

Tuto problematiku standardně řeší jakýkoliv Dependency Injection container, který automaticky
vyřeší závoslosti a co je třeba volat první a poslední.

MvcCore záměrně neimplementuje Dependency Injection design pattern a maximálně jde 
způsobem této automatické inicializace. Je to z následujících důvodů:
- I když DI container používá cache, stojí další procesní čas,
- DI potřebuje více kódu a konfigurace => další tooling => další know how navíc pro start práce,
- MvcCore nepracuje asynchronně a prozatím nepoužívá PHP woekery, pro práci s modely se využívá design pattern Active Record,
  blokový kód a co nejjednodušší přístup.
  
Vývojář chce programovat nebo vydělávat pěníze? Chce vydělávat peníze tak, aby ho práce bavila
a aby denně neluštil ošklivý kód. MvcCore je navrženo tak, aby nevznikali dlouhé třídy,
kde nelze najít pohodlně závislost. Každý controller, model či cokoliv jiného
lze i v sebesložitější aplikaci strukturovat tak, aby to bylo v rámci doporučení coding standards.

Dependency injection zatím přináší syntaktickou pohodu, nebo je nutně třeba až pro aplikace používající PHP workery či asynchronní přístup. I testování nebo více
databázových serverů je standardně možné řešit jinak než pomocí DI. 

V praxi se setkávám spíše s testováním celého funkčního výsledku.
Unit testing je sice krásná věc, ale já potřebuji vědět, zda se uživatel
přihlásí, zda autocomplete napoví, zda se objednávka odešle apod.
Což není jen kód v PHP a jedné třídě. Proto raději preferuji Selenium testování.

Navíc vytváření controllerů nebo modelů lze jakkoliv svobodně rozšířit zdokumentovaným způsobem.
Pokud někdo chce implementovat Dependency Injection design pattern v MvcCore,
je to velmi snadno řešitelné v rámci aplikace, nebo si na to vývojář může napsat velmi 
jednoduchou extenzi. Taková je filosofie frameworku - neklást překážky a moci svobodně tvořit.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Provolání `Init()` metody na všech dětech
Zde nejsou myšleny děti jako potomci tříd, ale jako děti controlleru přidané 
pomocí `$controller->AddChildController($subController);`.

Pokud využíváme automatickou inicializaci pro sub-controllery (formuláře 
nebo datagridy, což jsou také jen rozšíření controlleru), je třeba i na nich zavolat 
standardní metodu `$subController->Init();`, aby i v nich proběhl životní cyklus
controlleru tak jako jinde. Právě toto zavolání `Init()` metody na sub-controllerech 
řeší volání rodičovské metody `parent::Init();`.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

[▲ o úroveň výš](../README.md)

<div class="prev-next">

[◀ předchozí: **Instancování a konstruktor**](./instancing-and-constructor.md)  
[▶ další: **Inicializace akcí controlleru**](./action-initialization.md)  

</div>