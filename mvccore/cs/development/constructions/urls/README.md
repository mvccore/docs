# Vytváření URL adres

## Obsah
- [**Úvod**](#úvod)
- [**Funkce pro generování URL adres**](#funkce-pro-generování-url-adres)
- [**Argumenty funkce `Url()`**](#argumenty-funkce-url)
- [**První argument - název routy**](#první-argument---název-routy)
  - [**První argument - název routy - příklad**](#první-argument---název-routy---příklad)
- [**První argument - controller a akce**](#první-argument---controller-a-akce)
  - [**První argument - controller a akce - příklad**](#první-argument---controller-a-akce---příklad)
- [**První argument - automatické hodnoty a priorita stylu**](#první-argument---automatické-hodnoty-a-priorita-stylu)
  - [**První argument - automatické hodnoty a priorita stylu - příklad**](#první-argument---automatické-hodnoty-a-priorita-stylu---příklad)
- [**Druhý argument - parametry URL**](#druhý-argument---parametry-url)
  - [**Druhý argument - systémové názvy**](#druhý-argument---systémové-názvy)
  - [**Druhý argument - příklady**](#druhý-argument---příklady)

## Úvod
URL adresy v aplikaci generujeme dvěma způsoby:
- 1. **podle nakonfigurovaných rewrite rout**:
  - adresy, které uživatel uvidí v adresním řádku,
- 2. **podle názvů controllerů a akcí**:
  - adresy, které uživatel nikdy neuvidí.

Rewrite routy definujeme při startu aplikace v souboru `Bootstrap.php`,
více o definici rout v sekci [**Routování dotazů - Definování rout v routeru**](../../dispatching/application/request-routing.md#definování-rout-v-routeru).

V kódu pak může jeden i druhý způsob vypadat např. takto:
```html
<header>
	<a href="<?=$url('home')?>" class="logo">
		<img src="/static/img/logo.png" alt="Company" />
	</a>
	<form action="<?=$url('Front\Auth:SignOut')?>" method="post" class="logout">
		<input type="submit" value="Odhlásit" />
	</form>
</header>
<nav>
	<ul>
		<li><a href="<?=$this->Url('home')?>">Domů<li>
		<li><a href="<?=$this->Url('products_list')?>">Produkty<li>
		<li><a href="<?=$this->Url('docs', ['path' => 'about'])?>">O nás<li>
		<li><a href="<?=$this->Url('docs', ['path' => 'contacts'])?>">Kontakt<li>
	</ul>
</nav>
```

Nikdy nevytváříme URL adresy přímo tímto způsobem, i když
jsou takto zcela totožné s předchozím výstupem. Kód se špatně 
refaktoruje, protože nelze dobře vyhledat všechny definice URL adres,
které bychom potřebovali případně změnit:
```html
<header>
	<a href="/" class="logo">
		<img src="/static/img/logo.png" alt="Company" />
	</a>
	<form action="/index.php?controller=front/auth&action=sign-out" method="post" class="logout">
		<input type="submit" value="Odhlásit" />
	</form>
</header>
<nav>
	<ul>
		<li><a href="/">Domů<li>
		<li><a href="/products">Produkty<li>
		<li><a href="/about">O nás<li>
		<li><a href="/contacts">Kontakt<li>
	</ul>
</nav>
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Funkce pro generování URL adres
Funkce pro generování url adres jsou rozmístěny napříč aplikací v různých kontextech,
všechny míří nakonec do jediného místa a vždy si můžeme funkci takto stejně 
kdekoliv získat přes statické volání aplikace v instanci routeru:
```php
$app = \MvcCore\Application::GetInstance();
$router = $app->GetRouter();

// volání hlavní metody pro vytváření URL adres je v routeru:
$homeUrl = $router->Url('home');
```

Veřejná funkce URL je jako alias přítomna automaticky v controlleru i ve všech view šablonách:
```php
...
$productsUrl = $controller->Url('products_list');
...
```
```php
...
<li><a href="<?php echo $this->Url('docs', ['path' => 'about']); ?>">O nás<li>
<li><a href="<?=$url('docs', ['path' => 'contacts'])?>">Kontakt<li>
...
```

Vyjímkou je veřejná funkce `Url()` na instanci routy (`\MvcCore\Route` - neplést si s routerem).
Tato funkce je interní a je volána zmíněnou funkcí `Url()` z routeru. Vytváří jen část finální 
URL adresy a tuto funkci na objektu routy nikdy nepoužívejte, pokud neprogrmaujete nový router.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Argumenty funkce `Url()`
Funkce `Url()` má dva argumenty:
```php
$router->Url(
	string $controllerActionOrRouteName = 'Index:Index',
	array<string,mixed> $params = []
): string;
```
- `$controllerActionOrRouteName` - kombinace zkráceného (nebo absolutního) zápisu názvu třídy controlleru a názvu akce,
- `$params` - pole parametrů url pro aplikaci, ale mohou zde být i speciální přepínače pro jiný tvar URL.

## První argument - název routy
Tento zápis pro generování URL využíváme pro URL adresy, které budou viditelné pro uživatele v adresním řádku prohlížeče.
Tyto adresy se nevyužívají pro jedno-souborové aplikace. Zápis je velmi jednoduchý, stačí jako hodnotu argumentu
použít jméno routy definované v routě, na kterou chci odkázat.

**Výhody volání s názvem routy**:
- kratší zápis volání funkce `Url()` - nemusím vždy vypisovat celé dlouhé jméno
  názvu controlleru plus název akce, což je lepší, protože definici routy zapisuji 
  jednou v `Bootstrap.php` ale volání `Url()` na routu používám po aplikaci vícekrát,
- snažší refactoring při změně názvu controlleru, akce nebo přesunu 
  souboru controlleru, není třeba měnit všechny volání funkce `Url()`, 
  ale pouze routu, kde jjsou controller nebo akce se změnou definovány,
- v budoucích plánech vývoje je i možnost nechat si podle nastavení rout překontrolovat,
  zda všechna volání funkce `Url()` má existující routu nebo zda obshuje všechny povinné parametry,

**Nevýhody volání s názvem routy**:
- pro každou URL si musíme vytvořit routu, což je snesitelné,
- aplikace bude o něco málo pomalejší, při routování bude probíhat 
  zachycováním správné rewrite routy podle regulárních výrazů,
  což se dá velmi dobře optimalizovat skupinami rout, více informací 
  o seskupování rout v sekci [**Routování dotazů - Seskupování rout a optimalizace**](../../dispatching/application/request-routing.md#seskupování-rout-a-optimalizace),
- aplikace není přenosná bez URL rewrite modulu webserveru
  což nás může trápit pouze u jedno-souborových aplikací, 
  ne u standardních webových aplikací.

Je třeba si navíc zvolit nějaký vhodný systém pojmenování rout, např. odělovat tématické celky podtržítky,
kdy jméno routy půjde snadno odvodit, aniž bych si při psaní volání funkce `Url()` musel 
znova otevírat definice všech rout a dívat se, jak jsou pojmenované.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### První argument - název routy - příklad
```php
<?php // ./App/Bootstrap.php
$router->AddRoutes([
    'home'                 => [
        'controllerAction' => 'Index:Index',
        'pattern'          => '/',
    ],
    'albums_list'          => [
        'controllerAction' => 'CdCollection:Index',
        'pattern'          => '/albums',
    ],
    'albums_create'        => [
        'controllerAction' => 'CdCollection:Create',
        'pattern'          => '/create',
    ],
    'albums_edit'          => [
        'controllerAction' => 'CdCollection:Edit',
        'pattern'          => '/edit/<id>',
        'constraints'      => ['id' => '\d+'],
    ]
]);
```
```php
<?php // ./App/Controllers/Index.php

namespace App\Controllers;

class Index extends Base {

	/**
	 * Render homepage with signin form.
	 * If user is already authenticated, redirect user to albums list.
	 */
	public function IndexAction (): void {
		if ($this->user !== NULL) {
			// vygeneruje hlavičku `Location: /albums`
			self::Redirect($this->Url('albums_list')); 
		}
		$this->view->title = 'CD Collection';
		$this->view->signInForm = \MvcCore\Ext\Auths\Basic::GetInstance()
			->GetSignInForm()
			->AddCssClasses('theme')
			// set signed in url to albums list by default:
			->SetValues([
				// vygeneruje url: `https://example.com/albums`
				'successUrl' => $this->Url('albums_list', ['absolute' => TRUE]),
			]);
	}
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## První argument - controller a akce
Tento zápis pro generování URL využíváme pro URL adresy, které nebudou nikdy vidět v adresním řádku uživatele.
Není to podmínkou a můžeme s klidným srdcem používat pouze tyto tvary adres, pokud nám nevadí, že uživatel 
bude mít v každé URL adrese dlouhý query string s controllerem, akcí a parametry. Tyto adresy výhradně používáme
pro jedno-souborové aplikace, kde je nutné odkázat na zabalenou aplikaci v témž souboru.

**Výhody volání s controllerem a akcí**:
- pro volání funkce `Url()` nemusím tvořit routu, 
  kdykoliv odkáži kamkoliv,
- aplikace bude rychlejší, nebude se zaobírat při routování 
  zachycováním správné rewrite routy podle regulárních výrazů,
- aplikace je přenostná bez URL rewrite modulu webserveru, 
  což je výhoda pouze pro jedno-souborové aplikace,

**Nevýhody volání s controllerem a akcí**:
- horší refactoring při změně názvu controlleru, akce nebo přesunu 
  souboru controlleru, bude třeba měnit všechny volání funkce `Url()`, 
  kde jsou controller nebo akce se změnou definovány,
  delší zápis volání funkce `Url()` - musím vždy vypisovat celé dlouhé jméno
  názvu controlleru plus název akce, což vede k opakujícímu se kódu, viz případný refactoring,
- v budoucích plánech vývoje je i možnost nechat si podle nastavení rout překontrolovat,
  zda všechna volání funkce `Url()` má existující controller nebo akci, ale už nelze 
  implementovat, zda volání `Url()` obshuje všechny povinné parametry, protože k němu není routa.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### První argument - controller a akce - příklad
```php
<?php

namespace App\Controllers;

use \App\Models,
	\App\Forms,
	\MvcCore\Ext\Form;

class CdCollection extends Base {

	protected ?Models\Album $album = NULL;

	protected function getCreateEditForm () {
		$new = $this->album === NULL ? 1 : 0;
		$form = new Forms\Album($this);
		$form
			->SetMethod(Form::METHOD_POST)
			->SetAction(
				// vygenerují se URL: '/index.php?controller=cd-collection&action=submit&new=1'
				//                    '/index.php?controller=cd-collection&action=submit&new=0'
				$this->Url(':Submit', ['new' => $new])
			)
			->SetSuccessUrl(
				// vygeneruje se URL: '/index.php?controller=cd-collection'
				$this->Url(':Index')
			)
			->SetErrorUrl($this->Url(
				// vygenerují se URL: '/index.php?controller=cd-collection&action=create'
				//                    '/index.php?controller=cd-collection&action=edit'
				$new ? ':Create' : ':Edit')
			);
		return $form;
	}

	protected function initAlbum () {
		$id = $this->GetParam('id', '0-9', NULL, 'int');
		if ($id !== NULL) 
			$this->album = Models\Album::GetById($id);
		if ($this->album === NULL) {
			$new = $this->GetParam('new', '0-1', FALSE, 'bool');
			if (!$new) 
				$this->RenderNotFound();
		}
	}

	public function CreateAction (): void {
		$this->view->title = "Create new album";
		$this->view->form = $this->getCreateEditForm();
		$this->Render('create-edit');
	}
	
	public function EditAction (): void {
		$this->initAlbum();
		$this->view->title = "Edit album: {$this->album->GetTitle()}";
		$form = $this->getCreateEditForm();
		$form->SetValues($this->album->GetValues());
		$this->view->form = $form;
		$this->Render('create-edit');
	}
	
	public function SubmitAction () {
		$form = $this->getCreateEditForm(TRUE);
		[$result] = $form->Submit();
		if ($result != Form::RESULT_ERRORS)
			$this->album->Save();
		$form->SubmittedRedirect();
	}
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## První argument - automatické hodnoty a priorita stylu
Následující příklad ukazuje používání definic rout v `Bootstrap.php`,
kde mají routy svoje vlastním pojmenováním (nikoliv pouze jména podle 
controlleru a akce) a chování routeru, který je přesto schopen generovat 
URL adresy pomocí rewrite rout, i když do metody `Url()` uvedu kombinaci 
controlleru a akce nebo pouze akci nebo i pouze controller.

Pokud záměrně vynechám název controlleru před dvojtečkou nebo název akce po dvojtečce,
doplní si router při generování URL název controlleru nebo název akce ne podle výchozího 
systémového názvu ale podle aktuálně vyřizovaného controlleru nebo akce.

Router vždy zkouší první parametr metody `Url()` hledat jako klíč v poli podle 
vlastních jmen rout a poté jako klíč v poli podle názvu routy ve stylu controlleru a akce.
Pokud nemá definované žádné routy nebo takto žádnou routu nenalezne, pak vytvoří URL
vždy jako query string s parametry `controller` a `action`.

## První argument - automatické hodnoty a priorita stylu - příklad
```php
<?php // ./App/Bootstrap.php
$router->AddRoutes([
    'home'                 => [
        'controllerAction' => 'Index:Index',
        'pattern'          => '/',
    ],
    'albums_list'          => [
        'controllerAction' => 'CdCollection:Index',
        'pattern'          => '/albums',
    ],
    'albums_create'        => [
        'controllerAction' => 'CdCollection:Create',
        'pattern'          => '/create',
    ],
    'albums_edit'          => [
        'controllerAction' => 'CdCollection:Edit',
        'pattern'          => '/edit/<id>',
        'constraints'      => ['id' => '\d+'],
    ]
]);
```
```php
<?php // ./App/Controllers/CdCollection.php

namespace App\Controllers;

class CdCollection extends Base {

	public function EditAction (): void {

		// `/`
		$this->Url('home');

		// `/`
		// - router najde rewrite routu alternativně ve skladu
		//   klíčovaném podle controlleru a akce
		$this->Url('Index:Index');

		// `/albums`
		$this->Url('albums_list');

		// `/albums`
		// - při volání metody Url() se chybějící controller dosadí ten,
		//   který je aktuálně vyřizován, ne systémový výchozí controller
		// - router pak najde rewrite routu alternativně ve skladu
		//   klíčovaném podle controlleru a akce
		$this->Url(':Index');

		// `/albums`
		// - při volání metody Url() se chybějící akce dosadí ta, 
		//   která je aktuálně vyřizována, ne systémová výchozí akce
		// - router pak najde rewrite routu alternativně ve skladu
		//   klíčovaném podle controlleru a akce
		$this->Url('CdCollection:');

		// `/albums`
		// - router najde rewrite routu alternativně ve skladu
		//   klíčovaném podle controlleru a akce
		$this->Url('CdCollection:Index');

		// `/index.php?controller=cd-collection&action=submit&new=1`
		// - při volání metody Url() se chybějící controller dosadí ten,
		//   který je aktuálně vyřizován, ne systémový výchozí controller
		// - tato routa není definovaná mezi rewrite routami
		$this->Url(':Submit', ['new' => 1]);

		// `/index.php?controller=cd-collection&action=submit&new=0`
		// - při volání metody Url() se chybějící controller dosadí ten,
		//   který je aktuálně vyřizován, ne systémový výchozí controller
		// - tato routa není definovaná mezi rewrite routami
		$this->Url(':Submit', ['new' => 0]);

		// `/create`
		// - při volání metody Url() se chybějící controller dosadí ten,
		//   který je aktuálně vyřizován, ne systémový výchozí controller
		// - router najde rewrite routu alternativně ve skladu
		//   klíčovaném podle controlleru a akce
		$this->Url(':Create');

		// `/edit/3`
		// - při volání metody Url() se chybějící controller dosadí ten,
		//   který je aktuálně vyřizován, ne systémový výchozí controller
		// - router najde rewrite routu alternativně ve skladu
		//   klíčovaném podle controlleru a akce
		$this->Url(':Edit', ['id' => 3]);

	}
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Druhý argument - parametry URL
Druhý argument funkce `Url()` je volitelný a slouží pro definici parametrů, 
které budou na cílové akci controlleru potřeba. Cílová akce, tedy veřejná metoda 
v controlleru nemusí mít a ani neměla by mít uvedené parametry jako svoje argumenty.

Při routování nedochází automaticky k přetypování parametrů z hodnot v URL 
na typy argumentů cílové metody akce v PHP. Navíc tento přístup je neflexibilní
a v praxi je třeba stejnou akci volat s různou sadou argumentů a mít svobodu.

Proto parametry z volané URL s v controlleru vývojář získává vzdy metodou `$controller->GetParam(...);`.

V poli parametrů URL mohou být klíče s jakýmkoliv názvem (kromě systémových názvů níže).
Obvykle se využívá syntaxe s oddělovačem slov podtržítko `_`, pomlčka `-` nebo tečka `.`.
Nevyužívá se camelCase ani PascalCase pojmenování a pro názvy parametrů se nepoužívá 
diakritika ani velká písmena a to proto, aby nedocházelo k překlepům malých a velkých znaků.
Protože mezi parametry mohou být i query string parametry, které může BFU přímo přepisovat.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Druhý argument - systémové názvy
Některé názvy parametrů (klíče v poli `$params`) jsou využívány systémem, proto 
prosím nepoužívejte ve svých názvech parametrů systémové názvy, které najdete 
v interface [**`\MvcCore\Router\IConstants`**](https://github.com/mvccore/mvccore/blob/master/src/MvcCore/Router/IConstants.php). 
Jinak byste je museli přepisovat jako konstanty na extendovaném routeru.

Systémové názvy parametrů jsou následující:
- `controller` (`$router::URL_PARAM_CONTROLLER`):
  - parametr slouží pro definici cílového controlleru,
- `action` (`$router::URL_PARAM_ACTION`):
  - parametr slouží pro definici cílové akce,
- `absolute` (`$router::URL_PARAM_ABSOLUTE`):
  - parametr slouží pro vygenerování absolutní url,
- `path` (`$router::URL_PARAM_PATH`):
  - parametr slouží pro definici cesty, pokud dojde k zobrazení chybové stránky,
- `host` (`$router::URL_PARAM_HOST`):
  - parametr slouží pro volitelnou definici doménového hosta, pokud je tvar routy absolutní,
- `domain` (`$router::URL_PARAM_DOMAIN`):
  - parametr slouží pro volitelnou definici celé domény, pokud je tvar routy absolutní,
- `tld` (`$router::URL_PARAM_TLD`):
  - parametr slouží pro volitelnou definici top level domény, pokud je tvar routy absolutní,
- `sld` (`$router::URL_PARAM_SLD`):
  - parametr slouží pro volitelnou definici second level domény, pokud je tvar routy absolutní,
- `basePath` (`$router::URL_PARAM_BASEPATH`):
  - parametr slouží pro volitelnou definici cesty k aplikaci (obskurní případy).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Druhý argument - příklady

Následující příklady budou vycházet z tohoto nastavení definice rout v `Bootstrap.php`:
```php
<?php // ./App/Bootstrap.php
$router->AddRoutes([
    'product_edit'         => [
        'controllerAction' => 'Eshops\Products\Edit:Index',
        'pattern'          => '/eshop/products/detail/<id>[/<tab>]',
        'defaults'         => [
            'tab'          => 'detail'
		],
		'constraints'      => [
			'id'           => '\d+',
			'tab'          => '[-a-z0-9]+'
		],
    ],
    'product_list'         => [
        'controllerAction' => 'Eshops\Products\Index:Index',
        'pattern'          => '/eshop/products[/<grid>]',
        'constraints'      => ['grid' => '.*'],
    ],
	// ...
	'invoicing_sim_edit'   => [
		'pattern'			=> '/invoicing-set/<id_invoicing>/edit/<id_sim>',
		'controllerAction'	=> 'Invoicings\Sims:Edit',
		'constraints'		=> [
			'id_invoicing'	=> '\d+',
			'id_sim'		=> '\d+'
		],
	],
	// ...
    'documents'            => [
        'controllerAction' => 'Front\Document:Index',
        'pattern'          => '/<path>',
        'constraints'      => ['path' => '.*'],
    ],
]);
```

Příklad ukazuje možnosti generování URL adres s povinnými i nepovinnými parametry.
```php
<?php // kontext controlleru:

// `/eshop/products/detail/3` - nejjednodušší příklad, parametr <tab> je nepovinný
$this->Url('product_edit', ['id' => 3]);

// `/eshop/products/detail/3/reviews` - zobrazí se detail produktu se záložkou reviews v popředí
$this->Url('product_edit', ['id' => 3, 'tab' => 'reviews']);
```

Příklad ukazuje situaci, že pokud je některý vyžadovaný parametr pro routu vynechám 
a parametr je v aktuálně dotazované stránce, umí si metoda `Url()` doplnit chybějící 
parametry a jejich hodnoty z aktuálně dotazovaných parametrů.
```php
<?php // kontext controlleru:

// /invoicing-set/10/edit/20 - dva parametry
$this->Url('invoicing_sim_edit', ['id_invoicing' => 10, 'id_sim' => 20]);

// Pokud bych byl v akci controlleru, která je již dotazována s parametrem `id_invoicing`
// a chtěl bych pouze vygenerovat URL na jinou fakturovanou SIM, mohu jeden z parametrů
// vynechat, pokud je aktuálně dotazován. Potřebný parametr pro vygenerování URL se 
// doplní automaticky podle aktuálně dotazovaných parametrů - takto tedy dostanu URL 
// pro stejnou fakturaci ale pro jinou SIM:
// `/invoicing-set/10/edit/30`   <---- hodnota 10 pro id fakturace se doplnila automaticky
$this->Url('invoicing_sim_edit', ['id_sim' => 30]);
```

Příklad ukazuje možnosti generování pokročilých rout v extenzích datagridů.
Takové generování rout není v jádře samotném, ale v extenzích `mvccore/extcontroller-datagrid*`.
```php
// `/eshop/products/1/100/filter-id-in-1;2;3/sort-id-a`
$this->Url('product_list', [
	'grid'	=> $grid->GridUrl([
		'filter'	=> [
			'id'	=> [
				'in'=> [1,2,3]
			]
		],
		'sort'		=> [
			'id'	=> true
		]
	])
]);
```

Příklad ukazuje definici URL pro "catch all" routu, uvedenou vždy jako poslední mezi routami.
```php
// `/about`
$this->Url('documents', ['path' => 'about']);

// `/contacts/stores`
$this->Url('documents', ['path' => 'contacts/stores']);
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

<div class="prev-next">

[předchozí: **Konstrukce aplikace**](../README.md)  
[další: **Controllery a sub-controllery**](../controller/README.md)  

</div>