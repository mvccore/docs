# Zpracování HTTP dotazu

Cestu průchodu zpracování požadavku lze rozdělit do zhruba tří oblastí:

- [**Cesta zpracování v aplikaci**](./application/README.md)
  - [**Start aplikace**](./application/app-start.md)
  - [**Průchod zpracování v aplikaci**](./application/app-dispatch.md)
  - [**Routování dotazů**](./application/request-routing.md)
  - [**Vlastní handlery klíčových událostí**](./application/custom-handlers.md)

- [**Cesta zpracování v controlleru**](./controller/README.md)
  - [**Životní cyklus controlleru**](./controller/lifecycle.md)
  - [**Instancování a konstruktor controlleru**](./controller/instancing-and-constructor.md)
  - [**Inicializace controlleru**](./controller/initialization.md)
  - [**Inicializace akcí controlleru**](./controller/action-initialization.md)
  - [**Příprava renderování controlleru**](./controller/pre-dispatching.md)
  - [**Volání akcí controlleru**](./controller/action.md)

- [**Renderování a odesílání**](./rendering/README.md)
  - [**Módy renderování**](./rendering/rendering-modes.md)
  - [**Renderování šablon**](./rendering/views-rendering.md)
  - [**Proměnné šablon**](./rendering/view-variables.md)
  - [**View helpery**](./rendering/view-helpers.md)
  - [**Výstupy z controlleru, typy odpovědí**](./rendering/controller-output.md)
  - [**Přesměrování a ukončování**](./rendering/redirecting-and-termination.md)
  - [**Odeslání hlaviček a obsahu**](./rendering/response-sending.md)
  - [**Ukončení sezení**](./rendering/session-saving.md)

---

<div class="prev-next">

[předchozí: **Používání příkladů**](../first-steps/examples/README.md)  
[další: **Cesta zpracování v aplikaci**](./application/README.md)  

</div>