# Životní cyklus controlleru

## Úvod

Pro každý správně masměrovaný požadavek [**routováním**](../application/request-routing.md) je v instanci `\MvcCore\Application` vytvořena nová instance třídy rozšiřující předka `\MvcCore\Controller`.

Na instanci controlleru se v instanci aplikace zavolá jedinná veřejná metoda `$controller->Dispatch()`,  
kterou se vyřeší uvnitř controlleru samotného celý životní cyklus controlleru. Je tedy možné 
si životní cyklus controlleru upravit podle svých potřeb přepsáním metody `$controller->Dispatch()` 
a případně i controller metody `$controller->CheckDispatchState()`, která s ní úzce souvisí.

Životní cyklus controlleru je poté dán především spuštěním těchto metod instance controlleru:
1. Instancování a konstruktor,
2. `public function Init (): void;`,
3. `public function <action>Init (): void;`,
4. `public function PreDispatch (): void;`,
5. `public function <action>Action (): void;`,
6. `public function Render (): string;`,
7. `public function Terminate (): void;`.

Metody s označením `<action>` jsou specifické pro routovanou akci a hodnota
`<action>` je routovaná akce v PascalCase syntaxy. Např. akce v url uvedená 
v query stringu jako `index.php?action=product-info` je přeložena zde 
do volání metod ve tvarech `$controller->ProductInfoInit();` a `$controller->ProductInfoAction()`.

Každý z těchto kroků životního cyklu controlleru je popsán v dalších kapitolách.

---

<div class="prev-next">

[předchozí: **Cesta zpracování v controlleru**](./README.md)  
[další: **Instancování a konstruktor controlleru**](./instancing-and-constructor.md)  

</div>