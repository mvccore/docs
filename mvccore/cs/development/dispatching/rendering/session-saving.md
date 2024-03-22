# Ukončení sezení

## Obsah
- [**Co je sezení**](#co-je-sezení)
- [**Kdy dochází k ukončení**](#kdy-dochází-k-ukončení)
- [**Činnosti pro ukončení sezení**](#činnosti-pro-ukončení-sezení)
- [**Příklad ukončení sezení**](#příklad-ukončení-sezení)

## Co je sezení
Sezení, neboli session uživatele je zjednodušeně proces, kdy dochází k identifikaci  
uživatele podle session id cookie (obvykle `PHPSESSID`) a podle tohoto id dochází 
k automatické inicializaci dat do super globální proměnné `$_SESSION`, kde můžeme 
používat data s hodnotami pouze pro konkrétního uživatele.

Sezení se může a nemusí nastartovat. Některé dotazy na aplikaci nepotřebují
znát nic o uživateli, který se ptá. V MvcCore aplikacích se sezení startuje 
vždy až když je třeba.

Pokud se sezení nastartuje a vývojář v něm něco zmnění, je třeba pro další 
dotazy na aplikaci data ze sezení uložit, aby byly k dispozici pro další 
požadavek v pořadí, který by případně sezení také mohl potřebovat.

Proces uložení hodnot sezení do úložiště a prodloužení platnosti cookies
pro identifikaci sezení nazýváme **ukončení sezení**.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Kdy dochází k ukončení
Při výchozím módu renderování s output bufferem do objektu `\MvcCore\Response`
se ukončení sezení standardně provede až v ukončování aplikace v `$app->Terminate()`.

Při módu renderování přímo na výstup dojde k ukončení sezení těsně před
zahájením renderování začátku layout šablony.

Při ostantích typech odpovědi je již ukončení sezení velmi specifické, 
více v sekci [**Výstupy z controlleru, typy odpovědí**](controller-output.md).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Činnosti pro ukončení sezení
Ukončení sezení zahrnuje následující činnosti:
- je třeba ověřit, zda bylo sezení započato (nastartováno):
  - pokud ano:
    - je třeba odeslat HTTP hlavičku se session id cookie,
	- je třeba regenerovat a odeslat HTTP hlavičku s CSRF 
	  cookie hodnotou, pokud se používá tento novější typ CSRF ochrany,
	- je třeba zaregistrovat uložení hodnot sezení v shutdown handleru
	  pomocí PHP funkce `register_shutdown_function()` (těchto 
	  handlerů může být v rámci vyřizování odpovědi zařazováno více
	  a to i knihovnami třetích stran. Proto je tento handler zařazen 
	  až ke konci vyřizování požadavku, aby se dostal opravdu až na 
	  poslední možné místo:
	  - v tomto handleru se serializují metadata MvcCore session namespaces
	  - zavolá se PHP funkce `session_write_close()`, která teprve volá
	    skutečný zápis hodnot do session úložiště,
      - je stále možné implementovat si vlastní handlery pro 
	    management session hodnot pomocí PHP funkce `session_set_save_handler()`,
  - pokud ne, nedělat nic.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Příklad ukončení sezení
Příklad vychází z kódu ukončovací funkce `$app->Terminate()` a vyjadřuje, 
kdy by mělo dojít k ukončení sezení. Kód počítá se scénářem výchozího 
renderovacího módu s output bufferingem a s obsahem zkompletovaným 
v objektu `\MvcCore\Response`:
```php
// ověření, zda již byly odeslány HTTP hlavičky:
if (!$this->response->IsSentHeaders()) {
	
	// pokud HTTP hlavičky nebyly odeslány, ukončíme 
	// nakonfigurovanou třídou jádra sezení:
	$sessionClass = $this->sessionClass;

	// ověříme, zda je to třeba - zda bylo vůbec sezení nastartováno:
	if ($sessionClass::GetStarted()) {

		// odešleme `Set-Cookie` hlavičku se sesson id a novými expiracemi
		$sessionClass::SendSessionIdCookie();

		// odešleme `Set-Cookie` hlavičku s CSRF ochranou s novou hodnotou a novými expiracemi
		// (metoda si sama rozpozná, zda se používá tento novější typ orchany):
		$sessionClass::SendRefreshedCsrfCookie();

		// zaregistrujeme handler pro uložení sezení přes PHP funkci 
		// `register_shutdown_function()`. V handleru se pak volá funkce
		// pro skutečné uložení hodnot - funkce `session_write_close()`:
		$sessionClass::Close();
	}

	// nyní můžeme odeslat všechny HTTP hlavičky:
	$this->response->SendHeaders();
}

if (
	// oveříme, zda ještě nebylo odesláno tělo odpovědi:
	!$this->response->IsSentBody() &&
	// a oveříme, zda HTTP metoda není pouze HEAD:
	$this->request->GetMethod() !== \MvcCore\IRequest::METHOD_HEAD
) {
	// nyní můžeme odeslat tělo odpovědi:
	$this->response->SendBody();
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

[▲ o úroveň výš](../README.md)

<div class="prev-next">

[◀ předchozí: **Odeslání hlaviček a obsahu**](./response-sending.md)  
[▶ další: **Pokročilejší konstrukce**](../../constructions/README.md)  

</div>