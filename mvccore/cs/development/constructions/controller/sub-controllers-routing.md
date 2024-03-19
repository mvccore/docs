## Routování akcí do sub-controllerů

# Obsah
- [**Úvod**](#úvod)
- [**Příklad před implementací**](#příklad-před-implementací)
- [**Příklad po implementaci**](#příklad-po-implementaci)
- [**Pořadí volání akcí obou controllerů**](#pořadí-volání-akcí-obou-controllerů)
- [**Rewrite routing**](#rewrite-routing)

# Úvod
Pro následující problematiku je třeba znát životní cyklus controlleru, více v sekci [**Životní cyklus controlleru**](../../dispatching/controller/lifecycle.md).

Někdy může nastat situace, kdy je třeba routovat některé akce do sub-controllerů
přes query string nebo pomocí rewritovaných rout a hlavní controller nechat dělat 
stále stejný postup při vyřizování odpovědi.

Tato situace se může hodit, když již máte hotový nějaký hlavní controller 
a potřebujete do něj přidat libovolnou další funkcionalitu:
- navíc chcete tuto funkcionalitu použít i jinde opakovaným způsobem,
- nechcete, aby se kód této nové funkcionality míchal příliš do hotového controlleru,
  nebo nechcete hlavní controller dělat dlouhý, složitý.

Příklad níž popisuje, jak volat na již hotové akci `index` hlavního controlleru další 
akci `edit` v sub-controlleru, aniž by se příliš měnil kód hlavního controlleru 
pomocí dvou URL adres:
- volání standardního chování akce `Index` - URL `/`,
- volání chování pro editaci v akci `Edit` - URL `/?action=edit`.

Je možné uvažovat volání jako `create`, `submit` apod., ale příklad níže se snaží být 
co nejjednodušší. Další metody je jednoduché přidat stejným způsobem a v nich si 
klidně submitovat formulář apod. Stejně tak lze použít přepisování URL pomocí rewrite rout.

Je možné si samozřejmě v hlavním controlleru vytvořit i potřebné akce
a v nich volat veřejné metody sub-controlleru přímo. Není to sice super cool automatické,
ale cizí vývojář má okamžitě přehled o tom, co kód dělá.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Příklad před implementací

Hlavní controller `./App/Controllers/Index.php` před implementací:
```php
<?php

namespace App\Controllers;

class Index extends Base {

	public function IndexAction () {
		// ... something implemented by default
	}
}
```

Šablona hlavního controlleru `./AppViews/Scripts/index/index.phtml` před implementací:
```php
<h1><?=$heading?></h1>
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Příklad po implementaci

Hlavní controller `./App/Controllers/Index.php` po implementaci sub-controlleru:
```php
<?php

namespace App\Controllers;

class Index extends Base {

	#region sub-controller

	protected $autoInitProperties = TRUE;

	/** @autoInit - starší kompatibilní zápis, nepřeba v PHP > 8.0 */
	#[\MvcCore\Controller\AutoInit]
	protected \App\Controllers\Indexes\SubControl $subControl;

	public function EditAction () {
		$this->IndexAction(); // zavolám stejné chování na hlavním controlleru
	}

	#endregion

	public function IndexAction () {
		$this->view->heading = 'Hi there!';
		// ... something implemented by default

		$this->Render('index'); // při akci `edit` je třeba stále renderovat `index.phtml`
	}
}
```

Šablona hlavního controlleru `./AppViews/Scripts/index/index.phtml` po sub-controlleru:
```php
<h1><?=$heading?></h1>
<?=$subcontrol?>
```

Nový sub-controller `./App/Controllers/Indexes/SubControl.php`:
```php
<?php

namespace App\Controllers\Indexes;

class SubControl extends \MvcCore\Controller {
	
	public function Init () {
		parent::Init();

		// zamezíme chybě nenalezené šablony na akci, 
		// kterou může mít parent controller
		if (!in_array($this->actionName, ['index', 'create', 'edit'], TRUE))
			$this->actionName = 'index';
	}
	
	public function IndexInit () {
	}

	public function EditInit () {
	}

	public function PreDispatch () {
		parent::PreDispatch();
	}
	
	public function IndexAction () {
	}

	public function EditAction () {
	}

	public function Render ($controllerOrActionNameDashed = NULL, $actionNameDashed = NULL) {
		return parent::Render($this->actionName);
	}
}
```
Sub-controller by podle svého kódu měl mít připravené tyto šablony,
protože nemá uvedenou žádnou fixní šablonu pomocí property 
`protected ?string $viewScriptPath`.
```
./App/Views/Scripts/indexes/sub-control/index.phtml
./App/Views/Scripts/indexes/sub-control/create.phtml
./App/Views/Scripts/indexes/sub-control/edit.phtml
```

Pokud bude mít nadřazený controller akci, kterou nemá sub-controller, je třeba mít 
v inicializaci sub-controlleru podmínku pro výchozí akci sub-controlleru.

Kód je sice z větší části řešen manuálně, ale je to kvůli tomu, že reaálný svět 
nikdy nebude mít pouze jedno řešení. Framework nechce svazovat vývojáře pouze jedním
řešením, proto nedělá více automatické práce, než je nutné.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Pořadí volání akcí obou controllerů
Můžeme volat dvě různé akce a v hlavním a sub-controlleru budou vyřizovány 
metody životního cyklu v tomto pořadí (odsazení naznačuje že volání jedné metody způsobí volání odsazené):
```php
// při volání url: `/`:
// inicializace:
$controller->Init();
	$subControl->Init();
// inicializace akce:
$controller->IndexInit(); // pokud by také existovala
$subControl->IndexInit();
// příprava renderování:
$controller->PreDispatch();
	$subControl->PreDispatch();
// volání akce:
$controller->IndexAction();
$subControl->IndexAction();
// renderování:
$controller->Render();
	$subControl->Render();

// při volání url: `/?action=edit`:
// inicializace:
$controller->Init();
	$subControl->Init();
// inicializace akce:
$controller->EditInit(); // pokud by také existovala
$subControl->EditInit();
// příprava renderování:
$controller->PreDispatch();
	$subControl->PreDispatch();
// volání akce:
$controller->EditAction();
	$controller->IndexAction();
$subControl->EditAction();
// renderování:
$controller->Render();
	$subControl->Render();
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Rewrite routing

Pokud je to nutné, je možné pro volání v sub-controlleru vytvořit i rewritovanou routu.
Samozřejmě je nutné pamatovat na to, že definice kontrolleru a akce musí vždy vyjadřovat,
co se má primárně volat na hlavním controlleru:

```php
// ./App/Bootstrap.php:
$router->AddRoutes([
	'home'					=> '/',
	'edit'					=> [
		'pattern'			=> '/edit',
		'controllerAction'	=> 'Index:Edit'
	],
	'create'				=> [
		'pattern'			=> '/create',
		'controllerAction'	=> 'Index:Create'
	],
]);
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

<div class="prev-next">

[předchozí: **Vytvoření a renderování sub-controllerů**](./sub-controllers-dispatching.md)  
[další: **Dočasné zprávy - flash messages**](./flash-messages.md)

</div>