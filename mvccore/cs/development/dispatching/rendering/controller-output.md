# Výstupy z controlleru, typy odpovědí

## Obsah
- [**Úvod**](#úvod)
- [**Kdy nastavit typ odpovědi**](#kdy-nastavit-typ-odpovědi)
- [**Odeslání HTML odpovědi**](#odeslání-html-odpovědi)
- [**Odeslání XML odpovědi**](#odeslání-xml-odpovědi)
- [**Odeslání JSON nebo JSONp odpovědi**](#odeslání-json-nebo-jsonp-odpovědi)
- [**Odeslání textové odpovědi**](#odeslání-textové-odpovědi)
- [**Vlastní typy odpovědi**](#vlastní-typy-odpovědi)
- [**Chybové typy odpovědi**](#chybové-typy-odpovědi)

## Úvod
Aplikace může odpovídat jakýmkoliv typem odpovědi. Pro nějčastější způsoby 
nabízí připravené cesty, které usnadní vývojáři život pro tyto případy:
- Odeslání HTML odpovědi
  - standardní renderování a odeslání odpovědi
- Odeslání XML odpovědi
  - nastavení jiného typu odpovědi, standardní renderování a odeslání odpovědi
- Přesměrování
  - bez renderování, předčasné ukončení, pouze odeslání HTTP hlaviček
- Odeslání JSON nebo JSONp odpovědi
  - serializace dat, bez renderování, ukončení a odeslání 
- Odeslání TEXTové odpovědi
  - serializace dat, bez renderování, ukončení a odeslání 
- Odeslání chybové odpovědi
  - ukončení aktuálního požadavku, odeslání chybové odpovědi konfigurovanou cestou

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Kdy nastavit typ odpovědi
Pokud dopředu víme podle routování dotazu, jaký bude typ odpovědi, 
můžeme ji nastavit ve svém základním controlleru v mětodě `Init()`:
```php
<?php

namespace App\Controllers;

class XmlApi extends \App\Controllers\Base {

	public function Init (): void {
		parent::Init();
		$this->response->SetHeader('Content-Type', 'application/xml');
	}
}
```

Pokud typ odpovědi dopředu nevíme, můžeme ji obdobně změnit kdykoliv do doby, 
než jsou odeslané HTTP hlavičky, ideálně nejpozději ve volání akce v controlleru.
Následující metody pro předvolené odesílání obsahu si definici výstupního 
typu řeší automaticky kromě výstupu typu XML nebo vlastního výstupu.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Odeslání HTML odpovědi
Tato odpověď je nastavena ve výchozím stavu aplikace pro většinu požadavků
v metodě `Init()` základního controlleru `\MvcCore\Controller`.
Proto tento typ výstupu nemusíme nijak explicitně řešit. Pomocí 
obou módů renderování se automaticky obsah poskládá a odešle klientovi.

Pokud chceme odeslat jen velmi krátkou HTML odpoveď,  
můžeme tak učinit v controlleru přímo metodou `HtmlResponse()`:
```php
$this->HtmlResponse(
	string $output, 
	bool $terminate = TRUE
): void;
```
Příklad je pouze ilustrativní a používá se pouze pro velmi malé odpovědi, kdy je 
renderování příliš složité na to, aby bylo nutné ho řešit kůli jednomu řádku kódu:
```php
$this->HtmlResponse(
	'<html><body>OK</body></html>'
);
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Odeslání XML odpovědi
Pokud potřebujeme odeslat klientovi obsah typu XML, je třeba pouze 
nastavit hlavičku `Content-Type` v objektu `\MvcCore\Response`
a poté pokračovat v renderování šablon s XML obsahem obvyklám způsobem
pomocí obou módů renderování:
```php
<?php // ./App/Controllers/Rss.php

namespace App\Controllers;

class Rss extends \MvcCore\Controller {

	protected $layout = 'rss'; // použije se ./App/Views/Layouts/rss.phtml

	public function PostsAction (): void {
		// výstupní typ mohu říci klidně až nyní, 
		// abych kvůli tomu nemusel vytvářet metodu Init()
		$this->response->SetHeader('Content-Type', 'application/rss+xml');
		
		// ... dále už stačí renderovat standardním způsobem:
		$this->view->posts = \App\Models\Posts::GetLatest(50);
	}
}
```
```php
// ./App/Views/Layouts/rss.phtml
<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0">
	<channel>
		<title>My Blog</title>
		<link>https://www.example.com</link>
		<description>Very impressive content:-)</description>
		<?=$this->GetContent()?>
	</channel>
</rss>
```
```php
// ./App/Views/Scripts/rss/posts.phtml
<?php foreach ($posts as $post): ?>
	<item>
		<title><?=$escapeXml($post->GetTitle())?></title>
		<link><?=$url('post_detail', ['path' => $post->GetPath()])?></link>
		<description><?=$escapeXml($post->GetDescription())?></description>
	</item>
<?php endforeach; ?>
```

Pokud chceme odeslat jen velmi krátkou XML odpoveď, kdy je proces 
renderování příliš složitý kvůli tak krátké odpovědi, 
můžeme tak učinit v controlleru přímo metodou `XmlResponse()`.
Metoda si sama nastaví HTTP hlavičku `Content-Type` na hodnotu `application/xml`:
```php
$this->XmlResponse(
	string $output, 
	bool $terminate = TRUE
): void;
```
Příklad je pouze ilustrativní:
```php
$this->XmlResponse(
	'<?xml version="1.0" encoding="UTF-8"?><error>Not found.</error>'
);
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Odeslání JSON nebo JSONp odpovědi
Dalším běžným způsobem odpovědi aplikace je odeslání dat ve formátu JSON nebo
dat serializovaných jako JSON a obalených voláním funkce zaslané v query string 
parametru `callback`, čemuž říkáme JSONp odpověď.

Nejčastěji máme nějaká data jako PHP `object|array` a potřebujeme je serializovat
do JSONu, odeslat a ukončit odpověď. Takovou odpoveď zajistíme v controlleru metodami:
- `JsonResponse()` pro JSON odpověď:
  - odešle čistý JSON,
  - sama si nastaví HTTP hlavičku `Content-Type` na hodnotu `application/json`,
- `JsonpResponse()` pro JSONp odpověď:
  - odešle JSON obalený voláním funkce zasllané v query string parametru `callback`,
  - sama si nastaví HTTP hlavičku `Content-Type` na hodnotu `application/javascript`.

```php
$this->JsonResponse(
	mixed $data, 
	bool $terminate = TRUE, 
	int $jsonEncodeFlags = 0
): void;

$this->JsonpResponse(
	mixed $data, 
	string $callbackParamName = 'callback', 
	bool $terminate = TRUE, 
	int $jsonEncodeFlags = 0
): void;
```

Obě metody serializují předaná data do JSONu a nastaví odpověď do objektu
`\MvcCore\Response` pro pozdějí odeslání v okamžiku uložení. Obě metody mají 
automaticky nastavené, že mají ihned poté vést instanci aplikace do metody
`$app->Terminate()` a vyhodit vyjímku `\MvcCore\Controller\TerminateException`.
Tato vyjímka také vede k ukončení vykonávání požadavku, ale pokud je již 
aplikace ukončená, činnosti se neopakují.

Příklad použití v controlleru (příklad postrádá potřebná ověření, je pouze ilustrativní):
```php
<?php

namespace App\Controllers\Eshops;

class Product extends \App\Controllers\Eshop {
	
	protected ReviewsAction (): void {
		$id = $this->GetParam('id_product', '0-9', NULL, 'int');
		if ($id === NULL) throw new \Exception('No product id.', 400);
		$reviews = \App\Models\Eshops\Products\Review::GetLatest($id, 10);

		$this->JsonResponse($reviews);
	}
}
```

Tento postup má ale za následek, že příliš velké kolekce dat se musí nejprve
dostat z databáze do PHP modelu, v modelu rozparsovat a inicializovat do PHP 
objektů a tyto objekty se poté musí zpět serializovat do JSONu pro odeslání 
k uživateli. V jeden moment tak máme v paměti 2x věliká a téměř stejná 
data - PHP objekty i serializovaná data v JSON stringu. Pokud je dat opravdu hodně,
může dojít k dosažení paměťového limitu a PHP proces se ukončí. Proto je pro velká 
data třeba postupovat způsobem níže. Příklad používá extenzi 
[**`mvccore/ext-model-db-mysql`**](https://github.com/mvccore/ext-model-db-mysql)
a postrádá potřebná ověření, je pouze ilustrativní:
- data se v modelu nenačtou ihned, ale vytvoří se streamovací iterator z databáze,
- v controlleru se seralizuje velká datová kolekce postupně po jedné instanci,
- odpověď se odesílá přímo uživateli do váýstupu, neskladuje se v `\MvcCore\Response`.

```php 
<?php // ./App/Models/Eshops/Products/Review.php

namespace App\Models\Eshops\Products;

use \MvcCore\Ext\Models\Db;

class Review extends Db\Models\MySsql {

	public static function GetLatest (int $idProduct, int $limit): Db\Readers\Streams\Iterator {
		return self::GetConnection()
			->Prepare("
				SELECT *
				FROM products_reviews 
				WHERE id_product = :id 
				ORDER BY review_created DESC 
				LIMIT 0, {$limit};
			")
			->StreamAll([':id' => $idProduct])
			->ToInstances(static::class);
	}
}
```
```php
<?php

namespace App\Controllers\Eshops;

class Product extends \App\Controllers\Eshop {
	
	protected ReviewsAction (): void {
		$id = $this->GetParam('id_product', '0-9', NULL, 'int');
		if ($id === NULL) throw new \Exception('No product id.', 400);
		
		// namísto naplnění všech objektů z DB do PHP vytvoříme pouze iterátor:
		$reviewsIterator = \App\Models\Eshops\Products\Review::GetLatest($id, 10);
		
		// nastavíme typ odpovědi:
		$this->response->SetHeader('Content-Type', 'application/json');

		// ukončíme sezení, pokud je to nutné:
		$sessionClass = $this->application->GetSessionClass();
		if ($sessionClass::GetStarted()) {
			$sessionClass::SendSessionIdCookie();
			$sessionClass::SendRefreshedCsrfCookie();
			$sessionClass::Close();
		}

		// odešleme HTTP hlavičky:
		$this->response->SendHeaders();

		// iterujeme do výstupu záznam po záznamu, každý řádek z DB je 
		// načten postupně do PHP objektu a serializován do JSONu a pak zapomenut:
		echo '{"reviews":[';
		$separator = '';
		$totalCount = 0;
		foreach (reviewsIterator as $productReview) {
			echo $separator, \MvcCore\Tool::JsonEncode($productReview);
			$totalCount++;
			$separator = ',';
		}
		// nakonec můžeme i odeslat celkový počet položek, který nebyl dopředu znám:
		echo '],"totalCount":' . $totalCount . '}';

		// ukončíme požadavek (některé již provedené operace ukončování se znovu provádět nebudou):
		$this->Terminate();
	}
}
```
Podobně můžeme postupovat pro jakkoliv dlouhé odpovědi jiných typů.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Odeslání textové odpovědi
Méně častým typem odpovědi je typ čistého textu. Jeho použití je stejné jako
s odpovědí typu JSON nebo JSONp. Odpověď je nejprve nastavena do objektu 
`\MvcCore\Response` a při ukončení požadavku odeslána klientovi.
Text odesíláme metodou `TextResponse()` a tato metoda si nastaví sama HTTP 
hlavičku `Content-Type` na hodnotu `text/plain`:
```php
$this->TextResponse(
	string $output, 
	bool $terminate = TRUE
): void;
```

Příklad je pouze ilustrativní:
```php
$this->TextResponse("
# Authentication:
LoginGraceTime 1m
PubkeyAuthentication yes
AuthorizedKeysFile %h/.ssh/authorized_keys
PasswordAuthentication no
");
```

Při odesílání pouze textového obsahu ale prosím dbejte 
na nastavené kódování odpovědi v HTTP hlavičce, příklad 
je opět pouze ilustrativní:
```php
<?php

namespace App\Controllers\Api;

class Document extends \App\Controllers\Api\Base {
	
	protected ContentAction (): void {
		$id = $this->GetParam('id_doc', '0-9', NULL, 'int');
		$doc = NULL;
		if ($id !== NULL) 
			$doc = \App\Models\Document::GetById($id);
		if ($doc === NULL)
			throw new \Exception('Document not found.', 404);

		// výchozí kódování odpovědi aplikací MvcCore je UTF-8, ale můžeme nastavit jiné
		$this->response->SetEncoding($doc->Getencoding());

		$this->TextResponse($doc->GetContent());
	}
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Vlastní typy odpovědi
Z aplikace můžeme odpovědět libovolným způsobem. 
Máme dvě možnosti podle velikosti odpovědi:
- kratší postup pro menší odpovědi:
  - nastavíme HTTP hlavičku typu odpovědi,
  - zapíšeme odpoveď do objektu `\MvcCore\Response`,
  - zavoláme `$controller->Terminate()`,
- delší postup pro větší odpovědi:
  - nastavíme HTTP hlavičku typu odpovědi,
  - ukončíme sezení, pokud je to nutné,
  - odešleme HTTP hlavičky,
  - odešleme odpověď přímo do výstupu,
  - zavoláme `$controller->Terminate()`.

Příklad odeslání vlastní menší datové odpovědi, příklad je ilustrativní:
```php
<?php

namespace App\Controllers;

class UserIcon extends \MvcCore\Controller {
	
	public function IndexAction (): void {
		// získáme ikonu uživatele z databáze:
		$svgCode = $this->user->GetIcon();

		// nastavíme HTTP hlavičky a tělo odpovědi:
		$this->response
			->SetHeader('Content-Type', 'image/svg+xml')
			->SetBody($svgCode);

		// zavoláme ukončení (dojde k odeslání):
		$this->Terminate();
	}
}
```

Příklad odeslání vlastní vetší datové odpovědi. Zde odesíláme soubor 
ke stažení, ale může jít i o velký obsah pro prohlížeč, pokud bychom 
např. vynechali hlavičku `Content-Disposition`:
```php
<?php

namespace App\Controllers;

class Invoice extends \MvcCore\Controller {
	
	public function IndexAction (): void {
		$id = $this->GetParam('id_invoice', '0-9', NULL, 'int');
		$invoice = NULL;
		if ($id !== NULL) 
			$invoice = \App\Models\Invoice::GetById($id);
		if ($invoice === NULL)
			throw new \Exception('Invoice not found.', 404);

		$fullPath = $invoice->GetFullPath();
		$fileName = $invoice->GetFileName();
		$fileSize = filesize($fullPath);

		// nastavíme typ odpovědi - dokument ke stažení:
		$this->response->SetHeaders([
			'Content-Description'		=> 'File Transfer',
			'Content-Type'				=> 'application/pdf',
			'Content-Transfer-Encoding'	=> 'binary',
			'Expires'					=> '0',
			'Cache-Control'				=> 'must-revalidate, post-check=0, pre-check=0',
			'Pragma'					=> 'public',
			'Content-Length'			=> (string) $fileSize,
			'Content-Disposition'		=> 'attachment; filename="' . $fileName . '"'
		]);

		// ukončíme sezení (pokud je to nutné):
		$sessionClass = $this->application->GetSessionClass();
		if ($sessionClass::GetStarted()) {
			$sessionClass::SendSessionIdCookie();
			$sessionClass::SendRefreshedCsrfCookie();
			$sessionClass::Close();
		}

		// odešleme HTTP hlavičky:
		$this->response->SendHeaders();

		// odešleme odpověd přímo na výstup:
		readfile($invoiceFullPath);

		// ukončíme požadavek (některé již provedené operace ukončování se znovu provádět nebudou):
		$this->Terminate();
	}
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Chybové typy odpovědi
Pro chybové odpovědi máme v Mvccore aplikaci několik možností:
- **můžeme zavolat controller metody `RenderError()` nebo `RenderNotFound()`**:
  - jde pouze o funkce pro zkrácení zápisu, které vykonávají:
    - `RenderError()` - vyhodí obecnou vyjímku s vlastním nebo výchozím textem `Server Error` a kódem `500`,
    - `RenderNotFound()` - vyhodí obecnou vyjímku s vlastním nebo výchozím textem `Not Found` a kódem `404`,
  - dojde k zalogování chyby,
  - odeslaná odpověď se odešle buď jako:
    - HTML odpověď pomocí error controlleru a error akce k tomu určené,
    - nebo pokud něco opět nevyjde, jen jako plaint text odpověď s textem chyby a HTTP kódem,

- **můžeme kdykoliv vyhodit svoji vyjímku**:
  - vyjímka by pro výchozí chování měla mít text, který nic bezpečnostního neprozrazuje,
    zobrazení textu na výstup můžeme případně ovlivnit ve své implementaci error akce,
  - vyjímce můžeme přiřadit vlastní HTTP stavový kód, často jako 400, 404, 500 nebo jinný,
  - dojde k zalogování vyjímky,
  - odeslaná odpověď se opět odešle buď jako:
    - HTML odpověď pomocí error controlleru a error akce k tomu určené,
    - nebo pokud něco opět nevyjde, jen jako plaint text odpověď s textem chyby a HTTP kódem,
  - tento způsob umožnuje navíc:
    - logovat svoje PHP typy vyjímek,
	- odesílat svoje chybové HTTP status kódy,

- **můžeme vytvořit/upravit chybové stránky**:
  - díky tomu můžeme uživateli odesílat pouze obecné chybové zprávy a HTTP status kód 
    a do textu vyjímek můžeme dát prakticky cokoliv, nic se nedostane uživateli,
  - více k výchozímu controlleru a jeho chybovým akcím je v sekcích: 
    - [**Průchod zpracování v aplikaci - Vyřízení případné vyjímky**](../application/app-dispatch.md#vyřízení-případné-vyjímky),
	- [**Routování dotazů - Výchozí controller a akce**](../application/request-routing.md#výchozí-controller-a-akce).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

<div class="prev-next">

[předchozí: **Escapování**](./escaping.md)  
[další: **Přesměrování a ukončování**](./redirecting-and-termination.md)  

</div>