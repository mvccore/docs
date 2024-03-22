# Přesměrování a ukončování

## Obsah
- [**Úvod**](#úvod)
- [**Funkce `Redirect()`**](#funkce-redirect)
- [**Příklad v controlleru**](#příklad-v-controlleru)
- [**Jiné případy přesměrování**](#jiné-případy-přesměrování)

## Úvod
Dalším velmi častým způsobem odpovědi aplikace je přesměrování uživatele na jinou stránku.
Jedná se o odeslání HTTP stavového kódu 3xx a hlavičky `Location` s cílovou URL.

K přesměrování uživatele by mělo docházet ideálně v inicializaci controlleru, kdy
dopředu raději zjistíme, že dál nemá smysl odpověď vykonávat, abychom uživatele přesměrovali
co nejdříve a nemusel zbytečně čekat. Důvody jsou obvykle nepřihlášený uživatel v administraci, 
přejmenovaná URL stránky apod. Přesměrovávat můžeme ale prakticky kdykoliv až do doby,
něž jsou odeslány HTTP hlavičky, tedy ideálně do doby vykonání akce controlleru.

Nejčastěji tuto úlohu řeší formuláře, ale pro formuláře ji nemusíte zajišťovat tímto 
způsobem, formuláře po submitu potřebují zavolat jinou metodu, která formulář uloží a přesměruje.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Funkce `Redirect()`
Pro přesměrování používáme v controlleru veřejnou statickou funkci `Redirect()`.
Její parametry jsou:
- `string $location` - cílová URL pro přesměrování, relativní nebo absolutní, 
- `int $code` - stavový HTTP kód, volitelný, výchozí hodnota je `303 See Other`,
  je možné použít vlasní `int` hodnotu nebo předvolené konstanty:
  - `\MvcCore\IResponse::MOVED_PERMANENTLY` - 301,
  - `\MvcCore\IResponse::SEE_OTHER` - 303,
- `?string $reason` - textový důvod, volitelný, odeslaný jako hlavička `X-Reason`, 
  kam je možné uvést pro vývojáře krátký text. Často se stává, že aplikace přesměruje
  a my nevíme proč. Díky tomuto parametru se nám snado dohledá původ.

```php
\MvcCore\Controller::Redirect(
	string $location, 
	int $code = \MvcCore\IResponse::SEE_OTHER, 
	?string $reason = NULL
): void;
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Příklad v controlleru
Následující příklad je zjednodušený pro prezentaci funkce redirect.
Jde o klasickou situaci, kdy controller, který se využívá jako předek
pro všechny controllery vyžadující přihlášeného uživatele v administraci
má ve své `Init()` metodě přesměrování na přihlašovací formulář, 
pokud se žádný objekt uživatele neinicializoval:
```php
<?php

namespace App\Controllers;

class Admin extends \MvcCore\Controller {
	
	protected Init (): void {
		parent::Init();
		if ($this->user === NULL) {
			self::Redirect(
				$this->Url('front_login'), // cílová URL
				\MvcCore\IResponse::SEE_OTHER,
				'Expired authentication'
			);
			// kód zde již nebude vykonán
		}
	}
}
```
Jakýkoliv kód po zavolání statické metody `Redirect()` již nebude nikdy vykonán
a nemusíme ho nijak řešit, protože dojde k ošetřené vyjímce typu 
`\MvcCore\Controller\TerminateException`, která navede instanci aplikace
do metody `$app->Terminate()`.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Jiné případy přesměrování
Přesměrovat aplikaci můžeme samozřejmě i odkudkoliv jinudy, nejjen z controlleru.
Obecně vzato k tomu musíme sehnat instanci aplikace `\MvcCore\Application` 
a instanci odpovědi `\MvcCore\Response`:
```php
// Získání singleton aplikace:
$app = \MvcCore\Application::GetInstance();

// sestavení cílové URL:
$router = $app->GetRouter();
$location = $router->Url('front_login');

// Kompletace odpovědi:
$response = $app->GetResponse();
$response
	->SetCode(\MvcCore\IResponse::SEE_OTHER)
	->SetHeader('Location', $location)
	->SetHeader('X-Reason', 'Expired authentication');

// Zavolání ukončování:
$app->Terminate();
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

[▲ o úroveň výš](../README.md)

<div class="prev-next">

[◀ předchozí: **Výstupy z controlleru, typy odpovědí**](./controller-output.md)  
[▶ další: **Odeslání hlaviček a obsahu**](./response-sending.md)  

</div>