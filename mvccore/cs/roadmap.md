# MvcCore Roadmap

- [x] Spuštění přes PHP server
  - `php -S 127.0.0.1:8000 index.php`

- [ ] Dokumentace

- [ ] Controller:
  - Pro skládání controllerů pomocí traitů by bylo dobré udelat v controlleru metody:
    - AddInitHandler(), 
    - AddPreDispatchHandler(), 
    - AddActionHandler(), 
    - AddTerminateHandler().

- [ ] Automatické generování typových tříd pro views pro napovídání v IDE.

- [ ] Router:
  - Kromě feature canonical redirect by bylo dobré implementovat i nějaký vývojářský 
    přepínač (standardně vypnutý), ktery by při vytváření URL pomocí `GetUrl()` kontroloval, 
    zda cilový kontroller/akce/routa existuji a připadně někam zapsal nebo vyhodil vyjímku.

- [ ] Refactoring interfaces
  - Některé interfaces některých rozšíření jsou iméstěny nestandardně
  - Interfaces jsou příliš obsláhlé, bude třeba je rozdělit do měnších celků.

- [ ] PHP statická analýza:
  - Zpracovat pomocí PHPStan i nejčastěji používané extenze framewoku a posléze všechny na level 6:  
    - mvccore/ext-form*
    - mvccore/ext-model*
    - mvccore/ext-controller*
    - mvccore/ext-router*

- [ ] Testy

- [ ] Třída HTTP Context
  - Ekvivalent třídy v .NET Core - HttpContext
  - Třída by měla obsahovat aplikační objekty, které se liší v requestech
  - Vše ostatní mito tuto třídu by mělo jít cachovat nebo by mělo umět existovat 
    v nezměněné podobě v paměti pro budoucí implementaci asynchronního fungování
  - Toto bude velká změna, měl by být nějaký major release.
  - Tato třída bude třeba pro implementaci DI a běhového prostředí [franken.php](https://frankenphp.dev/)

- [ ] Dependency Injection
  - Zhodnotit, za má nyní smysl, dokud nefunguje framwork asynchronně.
    - Ano má, pokud se bude implementovat běhové prostředí [franken.php](https://frankenphp.dev/)
  - Zhodnotit klady nové magie zápisu oproti základnímu poslání framworku a jak by to celé šlo zkrátit

- [ ] Implementovat asynchronní fungování celého framworku
  - Perfektní řešení, které celkem odpovídá maximálně možnému:
	- [franken.php](https://frankenphp.dev/)
  - Podívat se, zda by šlo i udělat vlastní server pomocí php-pm, 
    nebo react php nebo pomocí: https://amphp.org/
  - Bude třeba odstranit globání PHP záležitosti:
	  - setlocale(), default_timezone_set()...
	  - super globální proměnné
  - Buď implementovat jako async extenze nebo implementovat přímo do stávajícího kódu.
  - Toto bude velká změna, měl by být nějaký major release.

- [ ] Další rozšíření:
  - Session pro ukládání session do databáze
    - http://php.net/session_set_save_handler
  - Více cache extenzí:
    - simple filesystem cache nebo sqlite cache
    - http://www.php-cache.com/en/latest/
  - Výstupní cache (podobně jako Pimcore CMS)
  - Implementace latte šablon

- [ ] Příklady
  - YAML konfigurace samostatně, ne pouze jako zakomentovaný option v jiném příkladu.
  - Příklad pro databázový translator s kategoriemi.
  - Příklad, kde je více domén v jedné aplikaci.

---

[▲ o úroveň výš](../../README.md)

<div class="prev-next">

[◀ předchozí: **Seznam extenzí a repozitářů**](./extensions.md)  
[▶ další: **O frameworku**](./about.md)  

</div>
