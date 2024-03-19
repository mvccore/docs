# Odeslání hlaviček a obsahu

## Obsah
- [**Úvod**](#úvod)
- [**Odeslání HTTP hlaviček**](#odeslání-http-hlaviček)
- [**Odeslání těla odpovědi**](#odeslání-těla-odpovědi)
  - [**1. Odeslání vyrenderovaného obsahu při módu rendrování s output bufferem**](#1-odeslání-vyrenderovaného-obsahu-při-módu-rendrování-s-output-bufferem)
  - [**2. Odeslání přímo šablonou nebo vývojářem při módu renderování přímo na výstup**](#2-odeslání-přímo-šablonou-nebo-vývojářem-při-módu-renderování-přímo-na-výstup)

## Úvod

Při výchozím módu renderování s output bufferem do objektu `\MvcCore\Response`
se odesílání HTTP hlaviček a obsahu standardně provede až v ukončování aplikace v `$app->Terminate()`.

Při módu renderování přímo na výstup dojde k odesílání HTTP hlaviček těsně před
zahájením renderování začátku layout šablony a poté se postupně přímo odesílá obsah odpovědi.

Při ostantích typech odpovědi je již odesílání hlaviček a obsahu velmi specifické, 
více v sekci [**Výstupy z controlleru, typy odpovědí**](controller-output.md).

Navíc může dojít i k přesměrování, které se může stát kdykoliv při vyřizování požadavku, 
více o přesměrování v sekci [**Přesměrování a ukončování**](redirecting-and-termination.md).

Obecně lze říci, že k odeslání HTTP hlaviček a obsahu může dojít prakticky kdykoliv.
Proto pro tyto dvě události existují dva vlastní aplikační handlery, více o aplikačních 
handlerech v sekci [**Vlastní handlery klíčových událostí**](../application/custom-handlers.md#handlery-zpracování-požadavku).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

# Odeslání HTTP hlaviček
HTTP hlavičky se snaží být maximálně synchronizované s interním stavem HTTP hlaviček 
definovaných pomocí funkcí `header()` a `header_remove()`. Pokud tedy při vykonávání odpovědi
nastavíte nějakou HTTP hlavičku těmito dvěma funkcemi, při okamžiku odesílání se
HTTP hlavičky z interního stavu PHP aktualizují do objektu `\MvcCore\Response` 
a jsou odeslány všechny včetně těch, které již v objektu byli.

Přitom přednost mají hlavičky interně definované v PHP pomocí funkcí `header()`,
pokud by již nějaká hodnota stejné hlavičky byla definována v objektu `\MvcCore\Response`.

HTTP hlavičky můžeme v objektu `\MvcCore\Response` mazat, přidávat, přepisovat, celé přenastavit,
detekovat přítomnost, detekovat odeslání apod. Hlavičky `Content-Encoding` se automaticky 
detekují a přidávají za hodnotu hlavičky `Content-Type` i `; charset=...`, což nemusíme řešit.
Objekt odpovědi rovněž umí řešit hlavičky, které mohou být uvedeny i vícekrát apod.

Na konci každé odpovědi HTTP hlaviček je i vlastní MvcCore hlavička `X-MvcCore-Cpu-Ram`,
která odesílá pro aktuální okamžik čas zpracování odpovědi v milisekundách a maximální
velist použité paměti pomocí PHP funkce `memory_get_peak_usage()`.

Odeslání hlaviček probíhá illustrativně takto a dejě se obvykle automaticky:
```php
$app = \MvcCore\Application::GetInstance();

$res = $app->GetResponse();
$res
	->SetCode(200)
	->SetHeaders([
		'Content-Type' 	=> 'text/plain'
		'Cache-Control'	=> 'max-age=0, private, must-revalidate',
	])
	->SetBody('OK');

// odeslání pouze HTTP hlaviček:
$res->SendHeaders();
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

# Odeslání těla odpovědi
Odeslání těla odpovědi probíhá buď jako:
- 1. odeslání vyrenderovaného obsahu připraveného v objektu `\MvcCore\Response` 
     při módu rendrování s output bufferem,
- 2. odeslání přímo šablonou nebo vývojářem při módu renderování přímo na výstup,
     bez čehokoliv připraveného v `$response->GetBody()`.
Více o konkrétním průběhu obou módů renderování je v sekci [**Módy renderování**](rendering-modes.md).

Tělo odpovědi může být i soubor, který obvykle odesíláme nejlépe pomocí PHP funkce `readfile()`.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### 1. Odeslání vyrenderovaného obsahu při módu rendrování s output bufferem
Příklad je pouze ilustrativní, můžete ho využít pro vlastní konstrukce:
```php
$app = \MvcCore\Application::GetInstance();

$res = $app->GetResponse();
$res
	->SetCode(200)
	->SetHeaders([
		'Content-Type' 	=> 'text/plain'
		'Cache-Control'	=> 'max-age=0, private, must-revalidate',
	])
	->SetBody('OK');

// ... uložení sezení

// odeslání pouze HTTP hlaviček:
$res->SendHeaders();

// odeslání před-připraveného obsahu:
$res->SendBody();
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### 2. Odeslání přímo šablonou nebo vývojářem při módu renderování přímo na výstup
Příklad je pouze ilustrativní, můžete ho využít pro vlastní konstrukce:
```php
$app = \MvcCore\Application::GetInstance();

$res = $app->GetResponse();
$res
	->SetCode(200)
	->SetHeaders([
		'Content-Type' 	=> 'text/plain'
		'Cache-Control'	=> 'max-age=0, private, must-revalidate',
	]);

// ... uložení sezení

// odeslání pouze HTTP hlaviček:
$res->SendHeaders();

// odeslání obsahu:
echo 'OK';
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

<div class="prev-next">

[předchozí: **Přesměrování a ukončování**](./redirecting-and-termination.md)  
[další: **Ukončení sezení**](./session-saving.md)  

</div>