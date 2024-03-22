# Používání příkladů

S příklady MvcCore je třeba vždy postupovat podle `README.md` souboru v repozitáři.
Zpravidla stačí projekt naimportovat pomocí `composer create-project mvccore/example-*`
tak, jak je uvedeno v `README.md` a poté aktualizovat composer závislosti pomocí `composer update`.

Pokud příklady používají databázi, mají souborovou databázi v SQLite 
a také soubory SQL dumpů pro jiné implementace databáze.

Příklady se snaží být maximálně zpětně kompatibilní a jsou napsané co nejjednodušeji, 
aby nebyla prezentace kódu zaplevelena věcmi, které mohou na první pohled mást.

## Klasické příklady
- [Hello World](https://github.com/mvccore/example-helloworld)
- [Pig Latin Translator](https://github.com/mvccore/example-translator)
- [CD Collection - Basic](https://github.com/mvccore/example-cdcol-basic)
- [CD Collection](https://github.com/mvccore/example-cdcol)
- [CD Collection - Multi Language & Mobile](https://github.com/mvccore/example-cdcol-multilang-mobile)
- [CD Collection - Separated Document Root](https://github.com/mvccore/example-cdcol-separated-doc-root)
- [CD Collection - Extended Models](https://github.com/mvccore/example-cdcol-extended-models)
- [Forms](https://github.com/mvccore/example-forms)
- [Blog](https://github.com/mvccore/example-blog)
- [MariaDB Global Logs (datagrid)](https://github.com/mvccore/example-mariadb-global-logs)

## Příklady jedno-souborových aplikací
- [Hello World - Portable](https://github.com/mvccore/example-helloworld-portable)
- [Pig Latin Translator - Portable](https://github.com/mvccore/example-translator-portable)
- [CD Collection - Portable](https://github.com/mvccore/example-cdcol-portable)
- [Project Basic - Portable](https://github.com/mvccore/project-basic-portable)

---

[▲ o úroveň výš](../../README.md)

<div class="prev-next">

[◀ předchozí: **Struktura složek projektu**](../structure/README.md)  
[▶ další: **Zpracování HTTP dotazu**](../../dispatching/README.md)

</div>
