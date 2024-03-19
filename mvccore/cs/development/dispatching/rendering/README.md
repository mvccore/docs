# Renderování a odesílání

## Obsah
- [**Úvod**](#úvod)
- [**Účel renderování**](#účel-renderování)
- [**Průběh renderování**](#průběh-renderování)
- [**Obsah sekce**](#obsah-sekce)

## Úvod
Renderování je proces po vyřízení akce controlleru, kdy dochází 
k volání veřejné metody buď automaticky nebo vývojářem přímo v akci níže
a je zkompletováno tělo odpovědi aplikace do stringu:
```php
$controller->Render(?string $controllerOrActionNameDashed = NULL, ?string $actionNameDashed = NULL): string;
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Účel renderování

Renderování slouží především pro aplikační odpovědi typu HTML/XML, případně TEXT,
kdy to dává smysl. Renderování řeší problém specifických a opakujícíh se částí 
odpovědi aplikace, kdy např. v HTML potřebujeme opakovaně odesílat téměř totožnou 
hlavičku, začátek a konec HTML dokumentu ale jeho prostřední obsahovou část 
potřebujeme vždy specifickou. 

Odpovědi typu JSON, JSONp, nebo prostý TEXT zpravidla nerenderujeme dále 
popsaným způsobem ale používáme na ně specifické typy odpovědi, více 
v sekci [**Výstupy z controlleru, typy odpovědí**](./controller-output.md).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Průběh renderování

Renderování probíhá ve většině případů tak, že jsou nakonfigurované vykreslovací 
šalony naplněny hodnotami z controlleru a převedeny na `string` a ten nastaven 
do objektu odpovědi pro odeslání při ukončení vyřizování. 

Ve vyjímečně dlouhých případech odpovědí je renderování provedeno nejprve 
odesláním hlaviček a pak postupným renderováním šablon do odpovědi aplikace. 
Více o těchto případek v následující sekci [**Módy renderování**](./rendering-modes.md).

Při obou módech renderování je využit základní princip usnadnění práce
pro odesílání podobných hlaviček, začátku a konce těla odpovědi aplikace a specifických 
obsahových částí. Pro specifické obsahové části používáme tzv. 
[**vykreslovací šablonu akce (action view)**](./views-rendering.md#šablona-akce) 
a to je vždy automaticky obaleno další vykreslovací šablonou (pro téměř 
totožné začátky a konce odpovědi aplikace), které říkáme 
[**vykreslovací šablona layoutu (layout view)**](./views-rendering.md#šablona-layoutu). 
Tento princip renderování je běžný ve většině MVC frameworků.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Obsah sekce
- [**Módy renderování**](./rendering-modes.md)
- [**Renderování šablon**](./views-rendering.md)
- [**Proměnné šablon**](./view-variables.md)
- [**View helpery**](./view-helpers.md)
- [**Výstupy z controlleru, typy odpovědí**](./controller-output.md)
- [**Přesměrování a ukončování**](./redirecting-and-termination.md)
- [**Odeslání hlaviček a obsahu**](./response-sending.md)
- [**Ukončení sezení**](./session-saving.md)

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

<div class="prev-next">

[předchozí: **Renderování a odesílání**](../README.md)  
[další: **Módy renderování**](./rendering-modes.md)  

</div>