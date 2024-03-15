# Struktura složek projektu

Klasická webová aplikace v MvcCore má obvykle tuto strukturu složek:
```
./App                      - povinná, složka obsahující aplikační PHP scripty
    ./Controllers          - povinná, složka s třídami controllerů, neomezené zanoření
    ./Models               - volitelná, složky s třídami modelů, neomezené zanoření
    ./Routers              - volitelná, složka s extendovanou třídou routeru nebo routy a továrny
    ./Views                - povinná, složky s podsložkami vykreslovacích šablon
        ./Helpers          - volitelná, složka obsahující vlastní implementace pomocných funkcí šablon
	    ./Forms            - volitelná, složka případných šablon formulářů
	    ./Layouts          - povinná, složka s šablonami layoutů
	        ./layout.phtml - výchozí soubor layoutové šablony
	    ./Scripts          - povinná, složka s šablonami akcí
    ./Bootstrap.php        - volitelná iicializační třída
    ./config.ini           - volitelný systémový config
./Libs                     - volitelná, složka s manuálně umístěnými knihovnami
./Var                      - volitelná, složka s dočasnými daty
    ./Logs                 - volitelná, složka se zalogovanými daty a vyjímkami
    ./Tmp                  - volitelná, složka s dočasnými aplikačníi soubory
./www                      - povinná, složka s document rootu web serveru
    ./index.php            - povinný, startovní script aplikace
    ./static               - volitelná, složka se statickým HTTP obsahem
        ./css              - volitelná, složka s kaskádovými styly
  	    ./js               - volitelná, složka s JavaScript soubory
  	    ./ts               - volitelná, složka s TypeScript zdrojovými soubory
        ./tmp              - volitelná, složka s dočasnými *.js nebo *.d.ts soubory
./vendor                   - volitelná, složka balíčků třetích stran při používání composeru
./composer.json            - volitelný, config balíčků třetích stran při používání composeru
.gitignore                 - volitelný, git config soubor při verzování repozitáře
```

Struktura aplikace pro jednosouborový projekt má pouze chybějící webserver document root `./www`
a všechny jeho složky jsou přesunuty o úroveň výš. Soubor `index.php` pak navíc obsahuje 
definici konstanty `MVCCORE_APP_ROOT`, která v klasických webových projektech není uvedena.

Názvy a cesty složek lze konfigurovat nastavením jádra aplikace v úvodu souboru `Bootstrap.php`.

---

<div class="prev-next">

[předchozí: **Založení projektu**](../new-project/README.md)  
[další: **Používání příkladů**](../examples/README.md)

</div>