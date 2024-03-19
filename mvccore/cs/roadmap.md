# MvcCore Roadmap

- Dokumentace

- Controller:
  - Pro skládání controllerů pomocí traitů by bylo dobré udelat v controlleru metody:
    - AddInitHandler(), 
    - AddPreDispatchHandler(), 
    - AddActionHandler(), 
    - AddTerminateHandler().

- Automatické generování typových tříd pro views pro napovídání v IDE.

- Router:
  - Kromě feature canonical redirect by bylo dobré implementovat i nějaký vývojářský 
    přepínač (standardně vypnutý), ktery by při vytváření URL pomocí `GetUrl()` kontroloval, 
    zda cilový kontroller/akce/routa existuji a připadně někam zapsal nebo vyhodil vyjímku.

- Spuštění přes PHP server
  - `php -S 127.0.0.1:8000` index.php`
  - PHPSAPI bude `cli-server` - a není jisté, zda všechny potřebné `$_SERVER` hodnoty budou známy.

- Refactoring interfaces
  - Některé interfaces některých rozšíření jsou iméstěny nestandardně
  - Interfaces jsou příliš obsláhlé, bude třeba je rozdělit do měnších celků.

- PHP statická analýza:
  - Zpracovat pomocí PHPStan i nejčastěji používané extenze framewoku a posléze všechny na level 6:  
    - mvccore/ext-form*
    - mvccore/ext-model*
    - mvccore/ext-controller*
    - mvccore/ext-router*

- Testy

- Třída Context
  - Ekvivalent třídy v .NET Core - HttpContext
  - Třída by měla obsahovat aplikační objekty, které se liší v requestech
  - Vše ostatní mito tuto třídu by mělo jít cachovat nebo by mělo umět existovat 
    v nezměněné podobě v paměti pro budoucí implementaci asynchronního fungování
  - Toto bude velká změna, měl by být nějaký major release.

- Dependency Injection
  - Zhodnotit, za má nyní smysl, dokud nefunguje framwork asynchronně.
  - Zhodnotit klady nové magie zápisu oproti základnímu poslání framworku.

- Implementovat asynchronní fungování celého framworku
  - Podívat se, zda by šlo udělat vlastní server pomocí php-pm, 
    nebo react php nebo pomocí: https://amphp.org/
  - Docela hezké řešení na první pohled:
	- https://frankenphp.dev/
  - Bude třeba odstranit globání PHP záležitosti:
	  - setlocale(), default_timezone_set()...
	  - super globální proměnné
  - Buď implementovat jako async extenze nebo implementovat přímo do stávajícího kódu.
  - Toto bude velká změna, měl by být nějaký major release.

- Další rozšíření:
  - Session pro ukládání session do databáze
    - http://php.net/session_set_save_handler
  - Více cache extenzí:
    - simple filesystem cache nebo sqlite cache
    - http://www.php-cache.com/en/latest/
  - Výstupní cache (podobně jako Pimcore CMS)
  - Implementace latte šablon

- Příklady
  - YAML konfigurace samostatně, ne pouze jako zakomentovaný option v jiném příkladu.
  - Příklad pro databázový translator s kategoriemi.
  - Příklad, kde je více domén v jedné aplikaci.