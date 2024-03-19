# View helpery

## Obsah
- [**Úvod**](#úvod)
- [**Helpery pro práci s šablonami**](#helpery-pro-práci-s-šablonami)
  - [**Include šablony layoutu**](#include-šablony-layoutu)
  - [**Include šablony akce**](#include-šablony-akce)
- [**Url helpery**](#url-helpery)
- [**Escapovací helpery**](#escapovací-helpery)
- [**Helpery MvcCore extenzí `mvccore/ext-view-helper-*`**](##helpery-mvccore-extenzí-mvccoreext-view-helper-)
- [**Vytvoření vlastního view helperu**](#vytvoření-vlastního-view-helperu)
  - [**Umístění vlastních helperů**](#umístění-vlastních-helperů)
  - [**Pojmenování vlastních helperů**](#pojmenování-vlastních-helperů)
  - [**Nejjednoduší helper**](#nejjednoduší-helper)
  - [**Standardní helper**](#standardní-helper)
    - [**Příklad standardního helperu**](#příklad-standardního-helperu)
    - [**Standardní vlastnosti**](#standardní-vlastnosti)
  - [**Helpery s funkcemi pro lokalizaci**](#helpery-s-funkcemi-pro-lokalizaci)
    - [**Příklad lokalizovaného helperu**](#příklad-lokalizovaného-helperu)
    - [**Lokalizační vlastnosti**](#lokalizační-vlastnosti)

## Úvod
Šablony obsahují několik základních pomocných funkcí, které nemusíte explicitně definovat
a jejichž názvy byste neměli používat pro své účely. Naleznete je jako volání closure 
lokálních proměnných i v kontextu šablony `$this`:

Některé helpery existují jako lokální closure proměnné, některé pouze 
jako funkce kontextu `$this` typu `\MvcCore\View`.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Helpery pro práci s šablonami
Funkce sloužící pro renderování dalších pod-šablon. Tyto funkce 
se velmi podobají klasickému PHP `include()`, jen se dvěma rozdíly: 
- první parametr je vždy relativní cesta od kořenového adresáře 
  šablon layoutů nebo od kořenového adresáře šablon akcí,
- nikdy neuvádíme koncovku `*.phtml` pro view šablony.
```php
// renderování další šablony layoutu, relativně od ./App/Views/Layouts
$this->RenderLayout(string $relativePath, array $variables = []): string;

// renderování další šablony akce, relativně od ./App/Views/Scripts
$this->RenderScript(string $relativePath, array $variables = []): string;
```
Pokud použiji v prvním parametru jako první znak(y) tečku `./` nebo dvě tečky `../`,
pak mohu definovat relativní cestu od šablony, kde je volání zapsáno.
Relativní cesta by nikdy neměla začínat přímo lomítkem. I na Windows používáme dopředná lomítka.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Include šablony layoutu
Příklad souboru `./App/Views/Layouts/layout.phtml`, který potřebuje 
includovat pod-šablonu `./App/Views/Layouts/header.phtml`, která je 
v křenovém adresáři layoutů:
```html
<div class="header">
	<?=$this->RenderLayout('header')?>
</div>
```
```html
<a class="logo" href="<?=$url('home')?>">
	<img src="/static/img/logo.png" alt="company logo" />
</div>
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Include šablony akce
Příklad souboru `./App/Views/Scripts/index/index.phtml`, který potřebuje  
několikrát relativně includovat pod-šablonu `./App/Views/Scripts/index/widget.phtml`:
```html
// ./App/Views/Scripts/index/index.phtml
<div class="widgets">
	<?php foreach ($this->widgets as $item): ?>
		<?=$this->RenderScript('./widget', ['widget' => $item])?>
	<?php endforeach; ?>
</div>
```
```html
// ./App/Views/Scripts/index/widget.phtml
<div class="widget">
	<h4><?=$widget->name?></h4>
	<p><?=$widget->content?></p>
</div>
```

Obě funkce vyhodí vyjímku, pokud cílový soubor nenaleznou a v textu vyjímky  
je zapsána cesta, kde měl soubor být. Proto je práce s těmito helpery velmi jednoduchá.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Url helpery
Funkce sloužící pro vytváření URLs podle rewrite rout nebo pomocí query stringu.
```php
// objektové volání do \MvcCore\View\UrlHelpers:
$this->Url(string $controllerActionOrRouteName = 'Index:Index', array $params = []): string;
$this->AssetUrl(string $path): string;
// zkrácené lokální closure funkce:
$url(string $controllerActionOrRouteName = 'Index:Index', array $params = []): string;
$assetUrl(string $path): string;
```
Helper `Url()` slouží pro standardní vytváření URL adres a jde o zkratku do funkce `Url()` v routeru.

Helper `AssetUrl()` slouží pro vytváření URL adres na JS/CSS soubory nebo obrázky, které budou 
zabaleny do jedno-souborové aplikace.

Více o vytváření URL adres v sekci [**Vytváření adres aplikace**](../../constructions/urls.md).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Escapovací helpery
Funkce sloužící pro XSS escapování podle kontextu, kde chci řetězec s uživatelským vstupem escapovat.

Helpery `Escape()`, `EscapeHtml()`, `EscapeAttr()` a `EscapeCss()` používají 
interně PHP funkci [**`htmlspecialchars()`**](https://www.php.net/manual/en/function.htmlspecialchars.php)
a jejich pokročilé parametry se vztahují k této funkci.

Helpery `EscapeJs()` a `EscapeAttrJs()` používají 
interně PHP funkci [**`json_encode()`**](https://www.php.net/manual/en/function.json-encode)
a jejich pokročilé parametry se vztahují k této funkci.

Kompletní dokumentace pro veškeré parametry funkcí napoví IDE nebo je lze dohledat v umístění:
- PHP zdroje: [**`\MvcCore\View\Escaping`**](https://github.com/mvccore/mvccore/blob/master/src/MvcCore/View/Escaping.php),
- PHP Docs: [**`\MvcCore\IView`**](https://github.com/mvccore/mvccore/blob/master/src/MvcCore/IView.php).
```php
// Escapování textu v HTML - <b><?=$this->Escape(...)?></b>
$this->Escape(string $str, int $flags = ENT_QUOTES, ?string $encoding = NULL, bool $double = FALSE, bool $jsTemplate = FALSE): string;
      $escape(string $str, int $flags = ENT_QUOTES, ?string $encoding = NULL, bool $double = FALSE, bool $jsTemplate = FALSE): string;

// Escapování uvnitř otevřeného HTML tagu - <tag  <?=$this->EscapeHtml(...)?>  >
$this->EscapeHtml(string $str, ?string $encoding = NULL, bool $double = FALSE): string;
      $escapeHtml(string $str, ?string $encoding = NULL, bool $double = FALSE): string;

// Escapování uvnitř HTML atributu - <tag attr="<?=$this->EscapeAttr(...)?>">
$this->EscapeAttr(string $str, int $flags = ENT_QUOTES, ?string $encoding = NULL, bool $double = FALSE): string;
      $escapeAttr(string $str, int $flags = ENT_QUOTES, ?string $encoding = NULL, bool $double = FALSE): string;


// Escapování dat do JS - <script>var data = <?=$this->EscapeJs(...)?>;</script>
$this->EscapeJs(mixed $obj, int $flags = 0, int $depth = 512): string;
      $escapeJs(mixed $obj, int $flags = 0, int $depth = 512): string;

// Escapování dat do HTML atributu - <input data-value="<?=$this->EscapeAttrJs(...)?>" />
$this->EscapeAttrJs(mixed $obj, int $flags = 0, int $depth = 512): string;
      $escapeAttrJs(mixed $obj, int $flags = 0, int $depth = 512): string;


// Escapování textu v XML - <node><?=$this->EscapeXml(...)?></node>
$this->EscapeXml(string $str, ?string $encoding = NULL, bool $double = FALSE): string;
      $escapeXml(string $str, ?string $encoding = NULL, bool $double = FALSE): string;

// Escapování uvnitř CSS - <style>.elm{color:<?=$this->EscapeCss(...)?>;}</style>
$this->EscapeCss(string $str): string;
      $escapeCss(string $str): string;

// Escapování textu v iCalendar - SUMMARY:<?=$this->EscapeICal(...)?>
$this->EscapeICal(string $str): string;
      $escapeICal(string $str): string;
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Helpery MvcCore extenzí `mvccore/ext-view-helper-*`
Pro všechny helper funkce níže je nutné doinstalovat composerem popsanou extenzi.  
Některé pomocné funkce jsou obsáhlejší a proto nejsou součástí jádra. 

Extenze view helperů jsou ale nadmíru užitečné a maximálně doporučované.
Obsahují pomocné funkce pro práci s JS/TS a CSS soubory pro bundling, minimalizace apod.,
obsahují formátování nejčastějších typů nebo obsahují funkce práci s textem.

Podrobnější seznam view helper extenzí a odkazy na své dokumentace nelzente v sekci [**Rozšíření a repozitáře MvcCore**](../../../extensions.md#rozšíření---pomocné-funkce-šablon)
```php
/**
 * Rozšíření pro vykreslování, minimalizaci a seskupování (nebo dočasnému ukládání) souborů
 * TypeScriptů/JavaScriptů, k přípravě všech podpůrných souborů stránky pro finální zabalení
 * a odeslání ke klientovi.
 * 
 * Extenze `mvccore/ext-view-helper-assets`.
 */
$this->Js(
	string $groupName = self::GROUP_NAME_DEFAULT
): \MvcCore\Ext\Views\Helpers\JsHelper;

$this->Css(
	string $groupName = self::GROUP_NAME_DEFAULT
): \MvcCore\Ext\Views\Helpers\CssHelper;

/**
 * Rozšíření pro formátovatování datumu nebo času pomocí 
 * PHP rozšíření `Intl` nebo pomocí staršího `strftime()`.
 * `
 * Extenze: `mvccore/ext-view-helper-formatdatetime`.
 */
$this->FormatDate(
	\DateTime|\IntlCalendar|int $dateTimeOrTimestamp = NULL, 
	int|string $dateTypeOrFormatMask = NULL, 
	int $timeType = NULL, 
	string|\IntlTimeZone|\DateTimeZone $timeZone = NULL, 
	int $calendar = NULL
): string;

/**
 * Rozšíření pro formátovatování celých nebo plovoucích čísel pomocí PHP rozšíření `Intl`
 * nebo pomocí konvencí lokalizace aplikace nebo pomocí explicitních nebo výchozích argumentů.
 * 
 * Extenze: `mvccore/ext-view-helper-formatnumber`.
 */
$this->FormatNumber(
	float|int $number = 0.0, 
	int $decimals = 0, 
	string $dec_point = NULL , 
	string $thousands_sep = NULL
): string;

/**
 * Rozšíření pro formátovatování finančních hodnot pomocí PHP rozšíření `Intl` nebo pomocí
 * konvencí lokalizace aplikace nebo pomocí explicitních nebo výchozích argumentů.
 * 
 * Extenze: `mvccore/ext-view-helper-formatmoney`.
 */
$this->FormatMoney(
	float|int$number = 0.0, 
	int $decimals = 0, 
	string $dec_point = NULL , 
	string $thousands_sep = NULL
): string;

/**
 * Rozšíření pro oříznutí čistého textu nebo textu s HTML značkami na maximální počet znaků.
 * 
 * Extenze: `mvccore/ext-view-helper-truncate`.
 */
$this->Truncate(
	string $text, 
	int $maxChars = 200, 
	bool $isHtml = NULL
):string;

/**
 * Rozšíření pro správné zpracování nezalomitelných mezer ve viditelném textu.
 * Možnost konfigurace pro libovolný jazyk.
 *  
 * Extenze: `mvccore/ext-view-helper-linebreaks`.
 */
$this->LineBreaks(
	string $text, 
	string $lang = ''
): string;

/**
 * Rozšíření pro zabránění čtení ciltivého obsahu před jednoduchými roboty pomocí převodu
 * obsahu na JS kód <script>document.write(String.fromCharCode(...));</script>.
 *  
 * Extenze: `mvccore/ext-view-helper-writebyjs`.
 */
$this->WriteByJS(
	string $string
): string;

/**
 * Rozšíření pro konverzi obsahu libovolného souboru podle zadaného relativního
 * nebo absolutního umístění ve formátu data URL: `data:image/png;base64,..`.
 *  
 * Extenze: `mvccore/ext-view-helper-dataurl`.
 */
$this->DataUrl(
	string $relativeOrAbsolutePath
): string;
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Vytvoření vlastního view helperu
V MvcCore je možné vyrobit pomocnou funkci pro veškeré view šablony velmi jednoduše
jako jednoduchou třídu s jednou veřejnou funkcí. Pro komplexnější pomocnou funkcionalitu 
šablon lze podědit některý view helper nebo rozšířit základní třídu view helperů, 
které sama o sobě nabízí základní stavbu helperů.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Umístění vlastních helperů
Všechny vlastní helpery mohou být umístněny přímo ve složce k tomu určené
`./App/Views/Helpers/*`. Třídy nesoucí tyto view helpery by tedy měli být 
v namespace `\App\views\Helpers\*`. Aplikace je ve výchozím stavu nakonfigurovaná 
tak, aby hledala nejprve v této složce a pokud tam pomocnou třídu nenajde, 
hledá v namespace `\MvcCore\Ext\Views\Helpers\*`. Tím je zajištěno, že lze
nainstalovat nějaký helper MvcCore extenze, ten podědit a používat primárně jeho
poděděnou verzi.

Pokud je třeba nakonfigurovat namespaces, kde se mají view helpery hledat,
je třeba při startu aplikace [**nastavit aplikaci v `Bootstrap.php`**](../application/app-start.md#bootstrapphp):
```php
$viewClass = $app->GetViewClass();

// zařadit namespace na začátek nejnižší prioritou:
$viewClass::PrependHelpersNamespaces('Any\\Other\\ViewHelpers\\Place', '...');

// zařadit namespace na konec nejnižší prioritou:
$viewClass::AppendHelpersNamespaces('Any\\Other\\ViewHelpers\\Place', '...');

// přenastavit namespaces view helperů, nejvyšší priorita na začátku:
$viewClass::SetHelpersNamespaces('Any\\Other\\ViewHelpers\\Place', '...');
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Pojmenování vlastních helperů
Pojmenování pro všechny třídy musí vycházet z následujícího schématu.  
Uvažujme pomocnou funkci, kterou budeme ve view šablonách používat jako `$this->Format(...);`:
- název souboru PHP třídy musí být v PascalCase s dodatkem `Helper` - `FormatHelper.php`,
- název PHP třídy musí být také v PascalCase s dodatkem `Helper` - `class FormatHelper {...}`,
- musí obsahovat veřejnou instanční metodu podle názvu helperu - `public function Format ();`.

Funkce helperu nemusí vracet string, může vracet instanci třídy na které můžeme volat další 
metody. Záleží na tom, jak budeme chtít helper ve view šablonách chtít používat.

Vlastní helpery jsou instancovány automaticky pro každý požadavek aplikace 
a to až když jsou potřeba a jejich vytvořená instance se používá 
do konce vyřízení požadavku pro všechny vykreslované šablony.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Nejjednoduší helper
Pro potřebu napsat velmi jednoduchou pomocnou funkci můžeme vytvořit třídu, 
která nebude nic dědit a bude mít pouze pomocnou funkci. Dopředu tento helper 
ale nelze nijak nastavovat:
```php
<?php

namespace App\Views\Helpers;

class FormatHelper {
	
	/**
	 * SIMPLE DEMO string formatter.
	 * Format any string template with curly bracket 
	 * replacements: '...{0}...{1}...', given as first param
	 * with params given as all next arguments
	 * @param  string $template string template with curly brackets replacements: '...{0}...{1}...'
	 * @param  mixed  $args...,  any arguments converted to string 
	 * @return string
	 */
	public function Format (string $template, mixed ...$args) {
		$arguments = func_get_args();
		if (count($arguments) == 0) return '';
		if (count($arguments) == 1) return $arguments[0];
		$str = array_shift($arguments);
		foreach ($arguments as $key => $val) 
			$str = str_replace('{'.$key.'}', (string)$val, $str);
		return $str;
	}
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Standardní helper
Proto, abychom mohli helper případně nějak nastavit již v úvodu požadavku aplikace,
např. podle lokalizace uživatele apod. Používáme proto třídu předka pro view helpery.

Tato třída se musí doinstalovat extenzí `mvccore/ext-view-helper`:
```sh
composer require mvccore/ext-view-helper
```

Implementace helperu musí mít:
- třídu předka jako `\MvcCore\Ext\Views\Helpers\AbstractHelper`,
- musí mít statickou vlastnost `protected static $instance = NULL;`.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

#### Příklad standardního helperu
Uvedený příklad převádí vstupní hodnotu na řetězec `Yes` nebo `No` podle toho,
zda vstupní hodnota může být `boolean` `TRUE` nebo `FALSE`.

```php
<?php

namespace App\Views\Helpers;

class	YesNoHelper
extends	\MvcCore\Ext\Views\Helpers\AbstractHelper {
	
	protected static $instance = NULL;
	
	protected $values = [
		'1'		=> 'Yes',
		'0'		=> 'No',
	];

	public function SetValues (array $values): static {
		$this->values = $values;
		return $this;
	}

	public function YesNo (?bool $bool): string {
		if ($bool === NULL) return '';
		$boolStr = $bool ? '1' : '0';
		return $this->values[$boolStr];
	}
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

#### Standardní vlastnosti
Tento view helper má ale pro své fungování k dispozici následující vlastnosti  
dozazené automaticky před svým prvním použitím v šabloně (před prvním voláním funkce `YesNo()`):
```php
// aktuálně vykreslovaný view objekt (v průběhu používání se automaticky mění):
$this->view;

// controller náležící k aktuálně vykreslovanému view objektu  (v průběhu používání se automaticky mění):
$this->controller;

// aplikační popisný objekt požadavku:
$this->request;

// aplikační objekt odpovědi:
$this->response;
```

Navíc můžeme tento view helper jakkoliv nakonfigurovat již v počáteční fázi aplikace.  
Např. nastavit hodnoty `Yes` a `No` na jiné texty, pokud je lokalizace uživatele německá:
```php
<?php

namespace App\Controllers;

class Base extends \MvcCore\Controller {

	public function PreDispatch (): void {
		parent::PreDispatch();
		if (!$this->viewEnabled) return;

		if ($this->user->GetLocalization()->GetLangCode() === 'de') {
			\App\Views\Helpers\YesNoHelper::GetInstance()
				->SetValues([
					'1' => 'Ja natürlich!',
					'0' => 'Gar nicht',
				]);
		}
	}
}

```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Helpery s funkcemi pro lokalizaci
Často je třeba view helpery nastavit mnohem sofistikovaněji podle potřeb lokalizace aplikace.
Pro tytp účely je třeba nainstalovat extenzi se základní třídou, která rozšiřuje možnosti view helperů ještě o něco více:
```sh
composer require mvccore/ext-view-helper-internationalized
```

View helper pak vytváříme takto:
- rozšířením třídy `\MvcCore\Ext\Views\Helpers\InternationalizedHelper`,
- opět implementací statické vlastnosti `protected static $instance = NULL;`.

Tuto třídu již používají extenze MvcCore pro formátování čísel, financí a datumů.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

#### Příklad lokalizovaného helperu
Rozšíření může ve výsledku vypadat velmi jednoduše puze podědením jiného rozšíření, které udělá mnoho práce za Vás.
Nebo můžete využít veškeré možnosti lokalizačních vlastností níže a naprogramovat vlastní formátování podmíněné nastavením lokalizace.
```php
<?php

namespace App\Views\Helpers;

class	FormatTimeHelper
extends	\MvcCore\Ext\Views\Helpers\FormatDateHelper {
	
	protected static $instance = NULL;

	public function FormatTime (\DateTime $dateTime): string {
		return parent::FormatDate(
			$dateTime, 
			'HH:mm:ss',
			\IntlDateFormatter::NONE,
			\IntlDateFormatter::NONE
		);
	}
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

#### Lokalizační vlastnosti
Tento view helper má pro své fungování k dispozici ještě další následující 
vlastnosti dozazené automaticky před svým prvním použitím v šabloně:
```php
/**
 * `boolean` `TRUE`, pokud je nainstalovaní 
 * PHP extenze `Intl`, jinak `FALSE`.
 * @var ?bool
 */
$this->intlExtensionFormatting;

/**
 * Aktuální lowercase kód jazyka získaný
 * z objektu \MvcCore\Request jako $req->GetLang().
 * @var ?string
 */
$this->lang;

/**
 * Aktuální uppercase kód země získaný 
 * z objektu \MvcCore\Request jako $req->GetLocale().
 * @var ?string
 * 
 */
$this->locale;

/**
 * Kód lokalizace ve tvaru `<jazyk>_<ZEMĚ>` 
 * podle předchozích hodnot.
 * @var ?string
 */
$this->langAndLocale;

/**
 * Pole (cache sklad) `Intl` formátovacích instancí
 * (datetime a number formátovače):
 * @var array<string,\IntlDateFormatter|\NumberFormatter>
 */
$this->intlFormatters = [];

/**
 * Výchozí kódování odpovědi aplikace, pokud není 
 * možné kódování získaz z objektu \MvcCore\Response.
 */
$this->defaultEncoding = 'UTF-8';

/**
 * Pole systémových kategorií pro PHP metodu `setlocale();` 
 * pro automatické nastavení národního prostředí systému 
 * před použitím v další šabloně.
 * @var array<int>
 */
$this->localeCategories = [LC_ALL];

/**
 * `TRUE`, pokud je třeba provádět jakoukoliv konverzi formátovaného výsledku
 * přes PHP transformaci `iconv()`. K tomuto stavu dochází, pokud je aktuální systémové 
 * kódování definované pomocí `setlocale()` jiné než kódování odpovědi aplikace. Tyto 
 * případy je nutné řešit pouze pokud se používá řešení mimo PHP extenzi `Intl`, tedy 
 * pomocí fallback funkcí jako `strftime()`, `number_format()` apod.
 * @var ?bool
 */
$this->encodingConversion;

/**
 * Aktuální systémové kódování definované pomocí PHP `setlocale()` podle 
 * první konfigurované kategorie v `$this->localeCategories`.
 * @var ?string
 */
$this->systemEncoding;

/**
 * Aktuální kódování odpovědi aplikace z objektu \MvcCore\Response.
 * @var ?string
 */
$this->responseEncoding;

/** 
 * Výchozí nastavení jazyka a země, pokud není provedeno žádné nastavení aplikací.
 * @var array<string>
 */
$this->defaultLangAndLocale = ['en', 'US'];
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

<div class="prev-next">

[předchozí: **Proměnné šablon**](./view-variables.md.md)  
[další: **Výstupy z controlleru, typy odpovědí**](./controller-output.md)  

</div>