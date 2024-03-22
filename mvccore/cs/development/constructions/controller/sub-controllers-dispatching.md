# Vytvoření a renderování sub-controllerů

## Obsah
- [**Vytváření**](#vytváření)
- [**Dependency Injection**](#dependency-injection)
- [**Konstruktor sub-controlleru**](#konstruktor-sub-controlleru)
- [**Vytváření - příklad - formulář**](#vytváření---příklad---formulář)
- [**Vytváření - příklad - hlavní navigace**](#vytváření---příklad---hlavní-navigace)

## Vytváření
Sub-controller lze vytvořit kdykoliv, počínaje automatickou inicializací vlastností controlleru.
Zpravidla jej však vytváříme v metodě `Init()` aktuálního conrolleru. Lze ho samozřejmě
vytvořit až v dané akci, která ho potřebuje. Tím se kód zjednoduší. Také se tím ale sub-controller 
dostává do stavu, kdy je jeho životní cyklus opožděn oproti aktuálnímu controlleru. MvcCore umí 
v případě nutnosti tento stav dohnat, viz. metoda `$controller->DispatchStateCheck();`.

Vytvářet sub-controllery pomocí automatické inicializace není nejvhodnější cesta. 
Protože ne na všech akcích controlleru budu potřebovat daný sub-controller vytvářet. 
Budu ho potřebovat pouze v některých akcích. 

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Dependency Injection
Pro takové potřeby se standardně využívá Dependency Injection Lazy Loading.
Jenže tento design pattern přináší příliš mnoho obtěžujícíh věcí navíc - accesory, 
továrny, konfigurace, bolesti hlavy:-) Pokud je DI skutečně zapotřebí, je možné ho 
v hlavních controllerech implementovat popsaným způsobem, více v sekci 
[**Instancování a konstruktor controlleru - Možnosti rozšíření chování**](../../dispatching/controller/instancing-and-constructor.md#možnosti-rozšíření-chování). 
Jediné co je třeba dodržet při instancování sub-controlleru je kontext
rodičovského controlleru, viz další odstavec.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Konstruktor sub-controlleru
MvcCore přístup pro instancování sub-controllerů se snaží být maximálně stručný a jednoduchý, 
proto se obvykle pro sub-controllery používá konstruktor, ve kterém se předává instance
nadřazeného controlleru a v konstruktoru se dosadí do property `$subController->parentController`,
která by v sub-controlleru nikdy neměla být `NULL`.

Toto předávání nadřazeného kontextu není nikde podmíněno žádným interface a je jen na 
vývojáři, jak bude situaci řešit. Tento přístup přináší nejjednodušší kód.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Vytváření - příklad - formulář
Příklad je ilustrativní, je zjednodušen, chybí mu model, některé další akce, potřebná ověřování a věnuje se pouze vytváření formuláře.
```php
// kontext hlavního controlleru, kde je vytvářen formulář
<?php

namespace App\Controllers;

use \App\Models,
	\MvcCore\Ext\Form,
	\MvcCore\Ext\ModelForms\Form as ModelForm;

class CdCollection extends Base {

	protected ?Models\Album $album = NULL;

	protected function getCreateEditForm (): ModelForm {
		$new = $this->album === NULL ? 1 : 0;
		$form = new ModelForm($this);
		$form
			->SetModelClassFullName(Models\Album::class)
			->SetMethod(Form::METHOD_POST)
			->SetAction($this->Url(':Submit', ['new' => $new]))
			->SetSuccessUrl($this->Url(':Index'))
			->SetErrorUrl($this->Url($new ? ':Create' : ':Edit'));
		if (!$new)
			$form->SetModelInstance($this->album);
		
		// create all form fields to work with them,
		// you can feel free to move form state formward...
		$form->Init();

		// now set up some form field dynamic restrictions:
		$yearField = $form->GetField('year');
		$yearField
			->SetMin(intval(date('Y')) - 500)
			->SetMax(date('Y'));

		return $form;
	}

	public function CreateAction (): void {
		$this->view->title = 'New album';
		$this->view->form = $this->getCreateEditForm();
	}

	public function EditAction (): void {
		$this->view->title = "Edit album - {$this->album->GetTitle()}";
		$this->view->form = $this->getCreateEditForm();
	}

	public function SubmitAction (): void {
		$form = $this->getCreateEditForm();
		list($result, $values, $errors) = $form->Submit();
		if ($result != $form::RESULT_ERRORS) {
			$this->album = $form->GetModelInstance();
			if (($result & $form::RESULT_SUCCESS_COPY) != 0) {
				$this->album
					->SetTitle($this->album->GetTitle() . ' - copy')
					->Save();
			}
		}
		$form->SubmittedRedirect();
	}
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Vytváření - příklad - hlavní navigace
Příklad je ilustrativní, je zjednodušen, chybí mu model, potřebná ověřování a věnuje se pouze vytváření sub-controlleru hlavní navigace.

```php
<?php // kontext hlavního controlleru, kde je vytvářena navigace

namespace App\Controllers;

class Base extends \MvcCore\Controller {

	protected ?\App\Controllers\Bases\MainNav $mainNav = NULL;

	public function Init (): void {
		parent::Init();
		
		$renderNavigations = (
			$this->viewEnabled && !$this->ajax &&
			$this->request->GetMethod() !== \MvcCore\IRequest::METHOD_POST
		);

		if ($renderNavigations)
			$this->mainNav = new \App\Controllers\Bases\MainNav($this);
	}

	public function PreDispatch () {
		parent::PreDispatch();
		if (!$this->viewEnabled) return;

		if ($this->mainNav !== NULL)
			$this->view->mainNav = $this->mainNav;
	}
}
```
```php
<?php // kontext sub-controlleru hlavní navigace

namespace App\Controllers\Bases;

class MainNav extends \MvcCore\Controller {

	/**
	 * Touto property mohu fixně namířit pro každou routovanou akci 
	 * renderování sub-controlleru vždy na jednu a tu samou šablonu.
	 */
	protected $viewScriptsPath = 'bases/main-nav';

	/**
	 * Pro vlastní implementaci DI je třeba do sub-controlleru inicializovat vždy 
	 * pouze tuto jedinnou povinnou property `parentController` a to jakoukoliv cestou,
	 * která nemusí být přes konstruktor. 
	 * Obvykle je v MvcCore přenášena instance rodičovského konstruktoru 
	 * konstruktorem sub-controlleru pro zjednodušení.
	 */
	public function __constructor (\MvcCore\Controller $parentController) {
		$this->parentController = $parentController;
	}

	public function PreDispatch (): void {
		parent::PreDispatch();
		// zjednodušení:
		$this->view->items = \App\Models\Bases\MainNav::GetAllItems();
	}
}
```
```html
// ./App/Views/Layouts/layout.phtml
<!DOCTYPE HTML>
<html>
	<head>
		<meta charset="UTF-8" />
		<title><?=$title?></title>
	</head>
	<body>
		<header>
			<!-- logotype, login, icons ...-->
		</header>
		<?=$mainNav?>
		<content>
			<?=$this->GetContent()?>
		</content>
	</body>
</html>
```
```html
// ./App/Views/Scripts/bases/main-nav.phtml
<nav>
	<ul>
		<?php foreach ($items as $item): ?>
			<li><a href="<?=$item->GetHref()?>"><?=$escape($item->GetText())?></a></li>	
		<?php endforeach; ?>
	</ul>
</nav>
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

[▲ o úroveň výš](../README.md)

<div class="prev-next">

[◀ předchozí: **Controllery a sub-controllery**](./README.md)  
[▶ další: **Routování akcí do sub-controllerů**](./sub-controllers-routing.md) 

</div>