# Renderování a odesílání

Renderování je proces po vyřízení akce controlleru, kdy dochází 
k volání veřejné metody buď automaticky nebo vývojářem přímo v akci:
```php
$controller->Render(?string $controllerOrActionNameDashed = NULL, ?string $actionNameDashed = NULL): string;
```

V této metodě je převedena vykreslovací šalona naplněná hodnotami na `string`
a nastavena do objektu odpovědi pro odeslání při ukončení vyřizování.

Renderování a odeslání odpovědi je nutné probrat v těchto krocích:
- [**Módy renderování**](./rendering-modes.md)
- [**Renderování šablon**](./views-rendering.md)
- [**Základní view helpery**](./view-helpers.md)
- [**Escapování**](./escaping.md)
- [**Manuální a automatický výstup controlleru**](./controller-output.md)
- [**Přesměrování a ukončování**](./redirecting-and-termination.md)
- [**Odeslání hlaviček a obsahu**](./response-sending.md)
- [**Uložení sezení**](./session-saving.md)

---

<div class="prev-next">

[předchozí: **Renderování a odesílání**](../README.md)  
[další: **Módy renderování**](./rendering-modes.md)  

</div>