# Vlastní handlery klíčových událostí

Zpracování HTTP dotazu se děje v těchto hlavních milnících, které jdou obvykle za sebou v pořadí níže. Někdy se může pořadí lišit, pokud dojde k nějaké vyjímce nebo např. přesměrování, pak mohou být některé milníky vynechány a zpracování http požadavku může přeskočit k bodu 5. a pokračovat dále.
1. [**inicializace v `Bootstrap.php`**](./app-start.md#bootstrapphp),
2. [**routování dotazů**](./app-dispatch.md#nalezení-routy-požadavku),
3. [**vytvoření controlleru**](./app-dispatch.md#vytvoření-instance-controlleru),
4. [**vyřizování controlleru**](./app-dispatch.md#vyřízení-životního-cyklu-controlleru) a [renderování],
5. [odeslání HTTP hlaviček],
6. [odeslání odpovědi], 
7. [**ukončení**](./app-dispatch.md#ukončení-požadavku).

TODO: všem odrazkam přidat odkazy

Mezi každý milník lze do aplikace přidat vlastní handler(y) a proces zpracování požadavku kdykoliv dokončit dříve (odeslání cache obsahu nebo přesměrování uživatele) nebo ukončit úplně.

Vlastní handlery zpracování požadavku jsou tak cestou, jak pohodlně ovlivnit a upravit průběh zpracování, aniž by bylo třeba třídy `\MvcCore\Application` a `\MvcCore\Response` extendovat, přeprogramovat a celému procesu detailně rozumět.

Tyto handlery mohou být přidány kdykoliv při běhu aplikace, pokud k milníku, ke které se vztahují ještě nedošlo. Pokud přidám handler pozdě, nevykoná se. Proto je dobré vlastní handlery zpracování požadavku přidávat v inicializaci v `Bootstrap.php`.

Více handlerů přidávaných do stejného místa zpracování požadavku lze mezi sebou číselně prioritizovat. Pokud dojde k zařazení handleru se stejným číslem (nebo bez čísla), rozhoduje pořadí inicializace handleru.

Do zpracování požadavku lze přidat tyto handlery:
```php
// mezi milníky 1. a 2. - po inicializaci objektů aplikace, před routováním:
$app->AddPreRouteHandler(callable $handler, ?int $priorityIndex = NULL): \MvcCore\Application;

// mezi milníky 2. a 3. - po routování, před vytvořením controlleru:
$app->AddPostRouteHandler(callable $handler, ?int $priorityIndex = NULL): \MvcCore\Application;

// mezi milníky 3. a 4. - po vytvoření controlleru, před vyřizováním contolleru a renderováním:
$app->AddPreDispatchHandler(callable $handler, ?int $priorityIndex = NULL): \MvcCore\Application;

// mezi milníky 4. a 5. - po vyřízení controlleru a renderování, před odesláním HTTP hlaviček:
$app->AddPreSentHeadersHandler(callable $handler, ?int $priorityIndex = NULL): \MvcCore\Application;

// mezi milníky 5. a 6. - po odeslání HTTP hlaviček, před odesláním odpovědi:
$app->AddPreSentBodyHandler(callable $handler, ?int $priorityIndex = NULL): \MvcCore\Application;

// mezi milníky 6. a 7. - po odeslání odpovědi, před ukončováním:
$app->AddPostDispatchHandler(callable $handler, ?int $priorityIndex = NULL): \MvcCore\Application;

// po milníku 7. - po ukončování, před zápisem sezení:
$app->AddPostTerminateHandler(callable $handler, ?int $priorityIndex = NULL): \MvcCore\Application;
```

Je možné přidat i ještě tyto dva speciální handlery pro start session.
Oproti předchozím handlerům ale není jisté, kdy přesně se spustí, neboď jejich
spuštění závisí na tom, která část procesovaného kódu aplikace si zrovna řekne 
první o nastartování session. Právě pro tyto účely jsou tyto handlery vhodné.
```php
// těsně před zavoláním `session_start()`, kdy je již vyřešeno session id:
$app->AddPreSessionStartHandler(callable $handler, ?int $priorityIndex = NULL): \MvcCore\Application;

// po nastartování session a po plné inicializaci sezení podle metadat:
$app->AddPostSessionStartHandler(callable $handler, ?int $priorityIndex = NULL): \MvcCore\Application;
```

Každý handler musí akceptovat tyto dva argumenty:
```php
function (\MvcCore\IRequest $request, \MvcCore\IResponse $response): bool|void {
	// ... obsah handleru
}
```

Pokud chci handlerem proces zpracování požadavku předčasně ukončit (a např. odeslat obsah výstupní cache), handler musí vrátit `boolean` `FALSE`, jinak nemusí vracet nic:
```php
function (\MvcCore\IRequest $request, \MvcCore\IResponse $response): bool|void {
	// ... obsah handleru
	$res->Send(); // dojde okamžitě k milníku 5. a 6.
	return FALSE; // dojde okamžitě k 7. milníku ukončování
}
```

Handlery lze prakticky jakkoliv ovlivnit průběh zpracování. Buďte s nimi však opatrní a s jejich používání šetřete. Spouštějí se totiž pro vyřízení všech požadavků aplikace a při neopatrném použití mohou negativně působit na výkon celé aplikace.

---

<div class="prev-next">

[předchozí: **Routování dotazů**](./request-routing.md)  
[další: **Cesta zpracování v controlleru**](../controller/README.md)  

</div>