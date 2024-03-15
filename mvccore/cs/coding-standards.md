# Coding Standards

Tento dokument popisuje pravidla a doporučení pro vývoj MvcCore a jeho extenzí. 
Při přispívání do kódu frameworku je třeba je dodržovat. 
Nejjednodušší způsob jak to udělat, je jednoduše napodobit již existující kód. 
Jde o to, aby veškerý kód byl stejný, protože PHP je dynamický (ne staticky typovaný) jazyk
a jeho refactoring je obtížnější. Často se stává, že je třeba ho fulltextově prohledávat
a stejná pravidla (byť pro sebemenší věci) pomohou najít, co je třeba.

MvcCore Coding Standard odpovídá PSR-12 Extended Coding Style s těmito výjimkami:
1. Začátek PHP souborů nepoužívá `declare(strict_types=1)`.
2. Pro public členy tříd a traitů se používá PascalCase pojmenování.
3. Pro odsazení se používají tabulátory.
4. Menší formátovací vyjímky.

### 1. Začátek PHP souborů nepoužívá `declare(strict_types=1)`
Pro co největší zpětnou kompatibilitu PHP nepoužíváme typování v PHP
v souborech a extenzích frameworku, tudíž by v těchto souborech přepínač ani nedával smysl. Jde sice o velký ústupek, ale zpětná 
kompatibilita, kterou tím získáváme, prozatím převažuje svoji hodnotou.
Kód framworku je pro případy, které řeší striktní typovaní zápisem `declare(strict_types=1)`, 
napsán uvnitř tak, že si případně více vstupních typů rozpozná a ošetří
podle toho, jak je zrovna potřeba.

### 2. Pro public členy tříd a traitů se používá PascalCase pojmenování
I když coding standards v PHP mají obvykle pro public a protected členy 
vždy camelCase jmennou konvenci názvů, nikdy jsem to nepovažoval za šťastné.
Jednoduše proto, že jsem nikdy neprogramoval jen v PHP:-) 
S nejlepší jmennou konvencí jsem se setkal v platformě .NET, 
kde je právě tato PascalCase naming konvence pro public členy tříd používaná.

Považuji to za užitečné při čtení cizího kódu. Zdržuje mne, když 
naleznu co hledám a pak zjistím, že metoda, kterou jsem chtěl použít 
je pouze protected a ne public:-) Musím pak třídu extendovat, kód řešit jinak apod.
Myslím, že není třeba obtěžovat vývojáře tím, že ho budu nutit někam scrollovat, 
aby zjistil modifikátor přístupu a pak se zase vracel tam kde byl.
Proto doufám, že tato jmená konvence bude vždy k užitku.

### 3. Pro odsazení se používají tabulátory
Tabulátory mají oproti mezerám tyto výhody:
- odsazování je smyslem znaku TAB, smysl znaku SPACE je rozdělení slov,
- velikost odstupu si lze v editoru přizpůsobit a nevnucují tak velikost odsazení,
- jejich psaní je rychlejší (jeden stisk klávesy),
- v poslední řadě jsou praktičtější pro lidi se zrakovým omezením:-).

### 4. Menší formátovací vyjímky

Pro deklaraci funkce používáme mezi názvem funkce a otevřenou kulatou závorkou mezeru.
Při volání funkce píšeme název funkce a otevřenou závorku dohromady.
Lze tak najít odděleně deklarace funkcí a volání funkcí a ne dostat 
oboje ve výsledcích hledání dohromady:
```
function myHandler () {}
myHandler();
```

Operátor zavináč `@` potlačující vypsání chyb na výstup musí být okomentován:
```php
$metaStr = @serialize($meta); // it may cause an error with data limit overflow
```

Dlouhé signatury funkcí obsahující více paramerů nebo dlouhé typy parametrů 
je třeba psát na více řádků:
```php
/**
 * @inheritDoc
 * @param  string   $ctrlClassFullName
 * @param  string   $actionNamePc
 * @param  string   $viewScriptFullPath
 * @throws \Exception
 * @return bool
 */
public function CreateController (
	$ctrlClassFullName, $actionNamePc, $viewScriptFullPath
) {
	// ...
}
```

K ohraničení řetězce by se měly používat jednoduché uvozovky (apostrof, `'`).
Zdvojené uvozovky by se měli použít pouze v těchto případech:
- string obsahuje znak jednoduchých uvozovek (apostrof, `'`),
- string obsahuje text, který bude zobrazován uživateli.
