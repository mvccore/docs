# Proměnné šablon

## Obsah
- [**Úvod**](#úvod)
- [**Předdefinované proměnné**](#předdefinované-proměnné)
- [**Proměnné v kontextu `$this` vs. lokální proměnné**](#proměnné-v-kontextu-this-vs-lokální-proměnné)
- [**Automatické napovídání proměnných v IDE**](#automatické-napovídání-proměnných-v-ide)
- [**Přepisování proměnných mezi přípravou renderování a akcí**](#přepisování-proměnných-mezi-přípravou-renderování-a-akcí)
- [**Dědění proměnných**](#dědění-proměnných)
- [**Sub-controllery a dědění proměnných**](#sub-controllery-a-dědění-proměnných)

## Úvod
Proměnné do vykreslovací šablony akce i layoutu se definují v controlleru do objetu instance `\MvcCore\View`,
který je od volání controller metody `PreDispatch()` sestaven do controller vlastnosti `$controller->view`.

Základní předání proměnné z controlleru do šablon akce nebo layoutu naleznete v sekci [**Volání akcí controlleru - Nastavení hodnot šablony**](./../controller/action.md#nastavení-hodnot-šablony).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Předdefinované proměnné
Šablony obsahují několik základních proměnných, které nemusíte explicitně definovat
a jejichž názvy byste neměli používat pro své účely:
```php
$viewPath;                        // string, cesta aktuálně vykreslovaného souboru šablony
$application, $this->application, // instance \MvcCore\Application
$environment, $this->environment, // instance \MvcCore\Environment
$controller,  $this->controller,  // instance \MvcCore\Controller
$request,     $this->request,     // instance \MvcCore\Request
$response,    $this->response,    // instance \MvcCore\Response
$this->__protected;               // instanční sklad interních proměnných objektu view 
```

V kterékoliv šabloně si můžete ručně zjistit předem známé hodnoty pomocí:
```php
// výpis celého objektu šablony:
<pre><?php var_dump($this); ?></pre>

// výpis proměnných kontextu šablony $this (bez view helperů):
<pre><?php var_dump($this->__protected['store']); ?></pre>

// výpis lokálních proměnných (včetně view helperů):
<pre><?php var_dump(get_defined_vars()); ?></pre>
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Proměnné v kontextu `$this` vs. lokální proměnné
Proměnné v šablonách mohou být využívány přes view kontext jako `$this->variable`
nebo jednodušeji jako lokální proměnná `$variable`. 

Zkrácený zápis pomocí `$variable` má ale omezení. Pokud v jednom souboru šablony vytvořím 
novou lokální proměnnou nebo změním existující lokální proměnnou, změní se pouze v daném souboru
šablony a ne v jiných šablonách, kde bych ji chtěl použít později, pokud jde o primitivní 
(nereferenční) datový typ proměnné - tedy `bool`, `int`, `float`, `string`, `null` a `array`.

Pokud použiji zápis `$this->variable` a vytvořím nebo změním proměnnou, bude nová hodnota proměnné 
známa i v jiných šablonách. Proto je zápis přes kontext `$this` vždy flexibilnější, ale protože využívá
PHP magickou funkci `__get()`, je i o něco pomalejší, pokud ho budete používat v nějakém cyklu opakovaně.

Získávání proměnných přes `$this` kontext pomocí PHP magické funkce `__get()` přináší ještě jeden benefit.
Lze přistupovat k `public`, `protected` i `private` vlastnostem controlleru přímo z view,
aniž byste je museli do view z controlleru explicitně definovat. Je to užitečné pro případ, že jste na 
něco zapoměli nebo pokud pracujete s nějakou komponentou třetí strany a její vývojář nenastavil 
do view vše, co jste potřebovali. Opět je to zde ale zaplaceno tím, že magické funkce budou o něco pomalejší.

Pokud to tak děláte běžně, ztratíte tak více přehled o tom, co vše šablona z controlleru potřebuje definovat.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Automatické napovídání proměnných v IDE
Pokud použiji zápis `$this->variable`, mohu si nechat napovědět proměnnou pomocí speciálně vyrobené 
třídy pro tento účel, která bude pro vývojáře definovat, co šablona potřebuje za proměnné.
Taková třída může ležet kdekoliv a bude sloužit pouze pro IDE, nikdy se nebude nijak interpretovat.
Je dobré jí dát nějakou specifickou koncovku - např. `*.d.php` a umístit ji vždy vedle souboru šablony,
tedy např. `./App/Views/Scripts/index/index.d.php`:
```php
<?php

namespace App\Views\Scripts\index;

class index extends \MvcCore\View {
	public string $variable;
}
```

V úvodu šablony `./App/Views/Scripts/index/index.phtml` pak otaguji pomocí PHP Docs kontext `$this` tak, 
aby mi IDE napovědělo `$this->variable`:
```php
<?php /** @var \App\Views\Scripts\index\index $this */ ?>
<b><?=$this->variable?></b>     <-- po stisknutí [SPACE] IDE automaticky napoví
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Přepisování proměnných mezi přípravou renderování a akcí
Běžnou praxí je definovat v controlleru v metodě `PreDispatch()` výchozí hodnoty 
a v jednotlivých akcích tyto hodnoty proměnných v šabloně nastavit do specifičtější podoby. 
Např. hodnota pro HTML `<meta name="robots" />`, kde potřebuji na některých stránkách 
hodnotu `index, follow` ale ve vyjímečných případech `noindex, nofollow`:

```php
<?php

namespace App\Controllers;

class Index extends \MvcCore\Controller {

	public function PreDispatch () {
		parent::PreDispatch();
		if (!$this->viewEnabled) return;
		$this->view->title = 'My Blog';
		$this->view->metaRobots = 'index, follow';
	}

	public function IndexAction () {
		$this->view->title = 'Sometimes High';
	}

	public function LoginAction () {
		$this->view->title = 'Administration';
		$this->view->metaRobots = 'noindex, nofollow';
	}
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Dědění proměnných
Proměnné v šablonách se chovají tak, jako kdyby šlo o jeden jediný view kontext `$this` obsahující vždy stejnou sadu proměnných.
Praxe ukázala, že explicitní předávání proměnných jako parametry různých include volání mezi šablonami
jsou tak devastující činnost, že v MvcCore jsou využívány pouze pokud je to explicitně třeba. Jinak se snaží MvcCore
v šablonách znát kontext všech proměnných, které by mohl vývojář chtít použít a tak mu zajistit maximální komfort.

Šablona layoutu obsahuje při vykreslování stejné proměnné jako šablona akce a naopak:
- [**Mód renderování přes output buffering**](./rendering-modes.md#1-renderování-přes-output-buffering-do-mvccoreresponse)
  - Šablona akce se renderuje první, poté teprve šablona layoutu.
  - Při započetí renderování layout šablony si layout přebírá proměnné ve stavu po dokončení renderování šablony akce.
  - Pokud vyrobím v controlleru nebo v šabloně akce proměnnou, bude poté známa v celém kontextu šablony layoutu se stejnou hodnotou.
- [**Módu přímého renderování na výstup**](./rendering-modes.md#2-přímé-renderování-na-výstup)
  - Šablona layoutu se započne renderovat první, uvnitř ní se renderuje šablona akce a poté se dokončí zbytek šablony layout.
  - Při započetí renderování šablony akce přebírá šablona akce proměnné od aktuálního stavu z šablony layoutu.
    - Pokud se během vykreslování šablony akce hodnoty pozmění, jsou poté při dokončování šablony layoutu 
	  s pozměněnými hodnotami.
    - Musím k nim však pro takový účel změn přes view kontext `$this`, nikoliv přes zkrácený zápis lokálních proměnných.
  - Pokud tedy vyrobím v controlleru nebo v šabloně layoutu (na začátku před renderováním akce) proměnnou, 
    bude známa v kontextu šablony akce a po zbytek renderování layoutu.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Sub-controllery a dědění proměnných
Podobné přebírání skladu proměnných provádí i šablony sub-controllerů, formulářů a datagridů tak, 
aby se šablony chovaly jako jeden velký view kontext se všemi proměnnými. Když tedy renderuji
sub-controller v některé šabloně akce (převodem na string přes `__toString()` - `<?=$subController?>` 
nebo vlastním voláním metody `Render()` - `<?=$subController->Render()?>`), převezme si 
nově vytvářená šablona pro renderování obsahu sub-controlleru také sklad proměnných z právě 
renderované šablony. Nedojde ale k přepsání k případně stejně nazvaných proměnných 
v šabloně sub-controlleru.

Existuje vyjímka u přebírání view kontextu proměnných sub-controllerů, formulářů a datagridů. 
Kontext sub-controlleru, formuláře nebo datagridu již může obsahovat svoji stejně pojmenovanou proměnnou.
Sub-controllery si nastavují proměnné do svých sub-controller view objektů podle svého 
a počítá se s potřebou, že hodnoty definované sub-controllerem jsou přednější.

Pokud se tedy stane, že hlavní controller definuje stejný název proměnné do view jako 
některý sub-controller do svého view, je pak v kontextu view šablony sub-controlleru 
přítomna hodnota ze sub-controlleru a v šabloně akce nebo layoutu přítomna hodnota definovaná
z hlavního controlleru. Zde není žádoucí plné přenesení proměnných.
Všechny ostatní proměnné z definované z hlavního controlleru, které neexistují ve view
sub-controlleru jsou v sub-controller view automaticky známé.

Pokud je navíc třeba z nějaké šablony sub-controlleru nutné sáhnout do kontextu hlavní šablony, 
je vždy v každé sub-controller šabloně k dispozici možnost získat view kontext 
nadřazeného controlleru jako `$this->view` a do něj si sáhnout pro jakoukoliv proměnnou.
Pokud je hierarchie vykreslování controlerů a view vyšší, je možné volání řetězit jako 
`$this->view->view` a dostat se tak o více řádů šablon výš.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

<div class="prev-next">

[předchozí: **Renderování šablon**](./views-rendering.md)  
[další: **Základní view helpery**](./view-helpers.md)  

</div>