# Routování dotazů

## Obsah
- [**Routování pomocí query stringu**](#routování-pomocí-query-stringu)
- [**Routování pomocí přepisovaných adres (mod_rewrite)**](#routování-pomocí-přepisovaných-adres-url-rewrite)
- [**Routování z CLI**](#routování-z-cli)
- [**Výchozí controller a akce**](#výchozí-controller-a-akce)
- [**Definování rout v routeru**](#definování-rout-v-routeru)
  - [**Seskupování rout a optimalizace**](#seskupování-rout-a-optimalizace)
  - [**Definice cesty**](#definice-cesty)
  - [**Nepovinné sekce URL**](#nepovinné-sekce-url)
  - [**Definice controlleru a akce**](#definice-controlleru-a-akce)
  - [**Pojmenování routy**](#pojmenování-routy)
  - [**Parametry routy**](#parametry-routy)
  - [**Výchozí hodnoty parametrů**](#výchozí-hodnoty-parametrů)
  - [**Omezení parametrů**](#omezení-parametrů)
- [**Další features routeru**](#další-features-routeru)
  - [**Kanonické přesměrovávání**](#kanonické-přesměrovávání)
  - [**Koncové lomítko v URL adresách**](#koncové-lomítko-v-url-adresách)
  - [**Dynamické načítání rout z databáze**](#dynamické-načítání-rout-z-databáze)
- [**Rozšířené routery**](#rozšířené-routery)
- [**Ukládání rout do cache**](#ukládání-rout-do-cache)

## Routování pomocí query stringu

Aplikace v MvcCore nabízí možnost routovat dotaz do konkrétního controlleru a akce
pomocí předvolených query string parametrů `controller` a `action`.

Názvy těchto dvou parametrů tudíž není možné používat pro jiné chování vytvářené aplikace.

Toto routování je velmi užitečné pro jakékoliv dotazy na pozadí (AJAX) nebo POST 
dotazy submitů formulářů, kdy je později uživatel přesměrován na konečnou stránku odpovědi formuláře.
Zkrátka pro dotazy, které uživatel stejně nikdy neuvidí v adresním řádku prohlížeče.

##### Příklad

Potřebuji nasměrovat aplikaci do controlleru:
- `\App\Controllers\EshopOrder\Delivery`  
a do volání akce:
- `$controller->GetPriceAction()`.

Query string pro toto vyřízení požadavku může vypadat takto:  
`/?controller=eshop-order/delivery&action=get-price&id_service=10`

Definice takové url bez routy se pak vytváří v aplikaci např. takto:
```php
$controller->Url('EshopOrder\Delivery:GetPrice', ['id_service' => 10]);
```
&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Routování pomocí přepisovaných adres (URL rewrite)

Obvykle se u webových aplikací využívá přepisování (rewritování) URL adres, 
kde nejsou ošklivě vypadající query string parametry tak jako v předchozím odstavci
a obsah adresního řádku je uživateli více přívětivý.

Takové adresy pak z pohledu klienta vedou "jakoby" do neexistujících adresářů na serveru 
a je na nastavení webserveru, aby takové neexistující požadavky směroval do souboru `index.php`.
Více o nastavení směrování je v sekci [**Start aplikace**](./app-start.md#htaccess-webconfig) 
nebo je třeba googlit `apache module mod_rewrite configuration for beginners`.

Webový server poté předá adresu z adresního řádku do superglobální PHP proměnné `$_SERVER`,
kde si ji MvcCore framework přečte a na základě tohoto řetězce postaví instanci 
`\MvcCore\Request` podle které probíhá zachycení routy v momentě [**Routování požadavku**](./app-dispatch.md#nalezení-routy-požadavku).

Zachycená routa má pak v sobě od vývojáře definovaný controller a akci, 
kam požadavek v daném tvaru směrovat a jaká controller a volání akce vytvořit. Případně má definované chování, jak si hodnoty pro controller a akcí získat.

Pokud má routa fixne uvedený controller nebo akci a přesto je v URL adrese query string
obsahující parametr `controller` nebo `action`, výsledný controller nebo akce je
prioritne zvolena podle query stringu. Což nese samozřejmě nutnost pohlídat si 
v každé akci, zda mám všechny parametry, které budu v dalších činnostech nutně potřebovat,
aby nedošlo k chybě. Ale to by vývojář měl dělat vždy zcela automaticky, i kdyby tato priorita ve frameworku nebyla.

Definování rout se obvykle provádí v souboru `Bootstrap.php` jako jedna 
z posledních inicializací aplikace, více v sekci [**`Bootstrap.php`**](./app-start.md#bootstrapphp).

Způsoby definic rout jsou později v dalších odstavcích.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Routování z CLI

Nasměrování požadavků z příkazové řádky probíhá obdobně jako v případě query string parametrů, 
pouze s výjimkou, že místo znaku ampersand `&` se pro oddělování parametrů použije obyčejná mezera ` `:
```sh
// zavolání controlleru a akce:
// \App\Controllers\Eshop\Notifications::SendAction();
php index.php controller=eshop/notifications action=send id_order=123
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Výchozí controller a akce

Pokud není v URL žádný query string definující controller nebo akci nebo pokud
je v url pouze cesta `/` či `/index.php`, je třeba aplikaci nasměrovat na výchozí controller a akci.

Stejně tak při chybovém stavu je nutné vyřídit chybový controller 
a chybové akce pro nenalezenou stránku nebo obecnou chybu.

Níže je seznam výchozích hodnot pro výchozí controllery a akce v MvcCore:
- **Výchozí contoller** - `Index` (`./App/Controllers/Index.php`):
  - Controller je vyřizován, pokud není určen controller pomocí query stringu 
    nebo pokud nemá zachycená rewrite routa definován žádný controller.
  - Controller je také vyřizován, pokud dojde k neošetřené vyjímce v aplikaci
    a pokud controller má definované chybové akce k tomu určené.
- **Výchozí akce** - `Index` (`$controller->IndexAction();`):
  - Akce je vyřizována v jakémkoliv controlleru, pokud není v query stringu nebo zachycené routě uvedena žádná jiná akce.
- **Chybová akce 404** - `NotFound` (`$errorController->NotFoundAction();`):
  - Akce je vyřizována na chybovém controlleru, pokud je kód neošetřené vyjímky 404.
- **Obecná chybová akce**  - `Error` (`$errorController->ErrorAction();`):
  - Akce je vyřizována na chybovém controlleru, pokud je kód neošetřené vyjímky jiný než 404.

Výše uvedené výchozí hodnoty je možné konfigurovat v `Bootstrap.php` nebo v 
inicializaci nějakého úplně základního controlleru pomocí následujících metod:
```php
// nastavení názvu výchozího a chybového controlleru:
$app->SetDefaultControllerName(string $detaultControllerName): \MvcCore\Application;

// nastavení názvu výchozí akce:
$app->SetDefaultControllerDefaultActionName(string $defaultActionName): \MvcCore\Application;

// nastavení názvu chybové akce 404: 
$app->SetDefaultControllerNotFoundActionName(string $defaultControllerNotFoundActionName): \MvcCore\Application;

// nastavení názvu obecné chybové akce: 
$app->SetDefaultControllerErrorActionName(string $defaultControllerErrorActionName): \MvcCore\Application;
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Definování rout v routeru

Definování rout se obvykle provádí v souboru `Bootstrap.php` jako jedna 
z posledních inicializací aplikace, více v sekci [**`Bootstrap.php`**](./app-start.md#bootstrapphp).

Pokud chceme použít vlastní router, je třeba nejprve v inicializaci `Bootstrap.php`  
nastavit třídu jádra na náš router pomocí metody  
`$app->SetRouterClass(\My\Custom\Router::class);`
Více opět v sekci [**`Bootstrap.php`**](./app-start.md#postup-inicializace).

Nejprve je třeba v `Bootstrap.php` získat instanci routeru:
```php
$router = $app->GetRouter();
```

Poté už můžeme definovat routy některým z následujících způsobů:
```php
$router->AddRoutes([
    // nejjednodušší zápis routy
    'Index:Index'         => '/',
    'CdCollection:Index'  => '/albums',
    'CdCollection:Create' => '/create',
    // příklad routy s omezením metody
    'CdCollection:Submit' => [
        'pattern'         => '/save',
        'method'          => 'POST'
    ],
    // příklad routy s parametrem
    'CdCollection:Edit'   => [
        'pattern'         => '/edit/<id>',
        'constraints'     => ['id' => '\d+'],
    ],
    // příklad redirect routy
    'index-php'           => [
        'match'           => '#^/index\.php$#',
        'redirect'        => 'Index:Index'
    ]
]);
```

Routu můžeme také přidat nejjen jako definici pole ale i jako instanci:
```php
// PHP compatible syntax:
$homeRoute = (new \MvcCore\Route)
    ->GetController('Index')
    ->GetAction('Index')
    ->SetMatch('#^/(index\.php)?$#')
    ->SetReverse('/');
// PHP >= 8.0 syntax:
$productRoute = new \MvcCore\Route(
    pattern: '/eshop/product/<name>',
    controllerAction: 'Eshop\Product:Index'
);
$router->AddRoutes([$homeRoute, $productRoute]);
```

Routery i routy mají nepřeberné možnosti konfigurace, doporučuji si přečíst tyto PHP traity:
- Instancování rout:
  - [**`\MvcCore\Route\Instancing`**](https://github.com/mvccore/mvccore/blob/master/src/MvcCore/Route/Instancing.php)
- Nastavování instancí rout:
  - [**`\MvcCore\Route\GettersSetters`**](https://github.com/mvccore/mvccore/blob/master/src/MvcCore/Route/GettersSetters.php)
- Přidávání, odebírání a nastavování rout do routeru:
  - [**`\MvcCore\Router\RouteMethods`**](https://github.com/mvccore/mvccore/blob/master/src/MvcCore/Router/RouteMethods.php)

### Seskupování rout a optimalizace
Routy je možné seskupovat do skupin podle první sekce mezi lomítky. 
První sekce mezi lomítky jsou znaky v cestě URL od prvního do druhého nekoncového lomítka:
```php
// URL                  => název skupiny rout (název první sekce URL)
'/'                     => ''
'/about'                => ''
'/eshop/category/tvs'   => 'eshop'
'/eshop/order/chart'    => 'eshop'
'/blog/posts'           => 'blog'
'/blog/post/something'  => 'blog'
```

Práce routeru se tím významně urychlí,
neboď router nespouští všechny regulární výrazy rout pro dotazovanou URL ale pouze regulární 
výrazy v rámci své skupiny. Pokud v URL není žádné první slovo ukončené nekončícím lomítkem,
je název skupiny pro routu prázdný string.

Je třeba takto seskupovat routy při přodávání do routeru manuálně:
```php
$router
    ->AddRoutes([
        'Index:Index'             => '/',
        'Index:About'             => '/about',
    ], '')
    ->AddRoutes([
        'Eshop\Category:Index'    => '/eshop/category/<category_name>',
        'Eshop\Order\Chart:Index' => '/eshop/order/chart',
    ], 'eshop')
    ->AddRoutes([
        'Blog:Post:List'          => '/blog/posts',
        'Blog:Post:Detail'        => '/blog/post/<post>',
    ], 'blog')
```

Pořadí vykonávání regulárních výrazů je vždy dané podle pořadí přidání routy do routeru.
Pokud se router rozhodně vykonat pouze regulární výrazy v nějaké skupině rout,
je pořadí v rámci skupiny rout opět dané pořadím při přidávání rout do routeru.

Nejčastější zápis je však ten nejkratší a to buď pomocí stringu nebo pole:-)

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Definice cesty

Definice cesty rewrite routy slouží pro automatické vytvoření regulárního 
výrazu pro zachycení aktuálně vyřizovaného požadavku podle URL a současně 
pro automatické vytvoření tvaru routy, podle kterého se mají v aplikaci vytvářet adresy, 
pokud chce danou routu někde použít a získat její URL adresu v aplikaci.

```php
$router->AddRoutes([
    'CdCollection:Create' => '/create',
    'CdCollection:Submit' => [
        'pattern'         => '/save',
        'method'          => 'POST'
    ],
]);
```

Vlastnost `pattern`, čili cestu routy, lze definovat buď jako `string`, kdy není potřeba
v routě cokoliv jiného nastavovat. Pojmenování routy se pak vezme jako klíč v poli,
které vkládám do metody `$router->AddRoutes()`.
To platí pro routu `CdCollection:Create`.

Nebo je možné nastavit `pattern` v definici routy jako člen pole definice routy.
Pak je možné o routě říci i další vlastnosti, jako třeba povolená HTTP metoda.
To platí pro routu `CdCollection:Submit`.

Pokud mi automatické vytvoření těchto dvou klíčových vlastností routy nevyhovuje, 
mohu definovat routu pomocí vlastního regulárního výrazu a podle vlastního vzoru URL současně. 
Regulární výraz v `match` slouží k zachycení routy podle dozatované URL a vzor URL 
v `reverse` slouží jako vzor, ze kterého se má v aplikaci poskládat URL podle této routy:

```php
$router->AddRoutes([
    'Index:Index'         => [
        //'pattern'       => '/',
        'match'           => '#^/(index\.php)?$#',
        'reverse'         => '/',
    ],
    'Color:View'          => [
        //'pattern'       => '/color/<color>',
        //'constraints'   => ['color' => '[0-9a-f]{6}'],
        'match'           => '#^/colou?r/(?<color>[0-9a-f]{6})/?$#',
        'reverse'         => '/color/<color>',
    ]
]);
```

V uvedeném příkladu je pro routu `Index:Index` použit vlastní regulární výraz pomocí 
vlastnosti `match` a vzor pomocí vlastnosti `reverse`, protože automaticky 
vygenerovaný regulární výraz umí zachytit pouze `/` ale neuměl by zachytit i možné 
dotazy ve tvaru `/index.php`.

Pro routu `Color:View` je pak také uveden vlastní regulární výraz,
který umí zachytit jak americký tvar `color` tak anglický tvar `colour`:-). 
Je pak ale třeba při použití parametrů v regulárním výrazu definovat i omezení pomocí `[0-9a-f]{6}`
a není tak možné využít vlastnost `constraints`. Vlastní výrazy v routách jsou ale super:-)

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Nepovinné sekce URL

Cesta routy definovaná pomocí vlastnosti `pattern` může obsahovat libovolný počet 
nepovinných sekcí, neboli částí cesty. Nepovinnou část cesty uzavíráme do hranatých závorek.
Hranaté závorky jsou tedy pro přímé používání v URL nevhodné, slouží pro označení volitelných sekcí cesty.
(Pokud i přesto bylo nutné definovat hranaté závorky jako součást URL, je třeba routě definovat
manuálně vlastnost `match` a `reverse`. Ve vlastnosti `match` bych tyto závorky escapovat jako `\[\]`).

Nepovinné sekce URL se nejčastěji používají pro definici nepovinných parametrů.
Ale lze je použít i jako volitelnou variantu cesty routy. Kanonická URL však bude ta bez volitelné sekce:
```php
$router->AddRoutes([
    // zachytí se `/color/ff00ff` ale i `/colour/ff00ff`, kanonická URL bude ta první
    'Color:View'      => [
        'pattern'     => '/colo[u]r/<color>',
        'constraints' => ['color' => '[0-9a-f]{6}'],
    ],
    // zachytí se `/blog/posts` ale i `/blog/posts/1/desc` apod., kanonická URL bude ta první
    'Blog:Posts'      => [
        'pattern'     => '/blog/posts[/page-<page>][/order-<order>]',
        'defaults'    => [
            'page'    => 1,
            'order'   => 'asc',
        ],
        'constraints' => [
            'page'    => '\d+',
            'order'   => 'asc|desc',
        ],
    ]
]);
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Definice controlleru a akce

Definice controlleru a akce v routě může být definovaná pomocí vlastností `controller`, 
`action` nebo dohromady pomocí souhrnné vlastnosti `controllerAction`. Pokud klíč v poli 
předávaném do metody `$router->AddRoutes()` obsahuje znak dvojtecka `:` a ani controller 
a ani ani nejsou v routě nijak specifikovány, je tento klíč použit jako hodnota pro vlastnost 
routy `controllerAction`. Definici `controller` nebo `action` je možné v routě vynechat 
a dodávat hodnotu pomocí parametru routy nebo pomocí query string parametru. 

Je třeba ale routu vždy konstruovat tak, aby nakonec měla znám controller i akci, na kterou 
má být směrována. Pokud pak controller nebo akce chybí, je dosazen výchozí název controlleru
nebo výchozí název akce z aplikace.
```php
$router->AddRoutes([
    'list'                 => [
        // definice pomocí vlastností `controller` a `action`
        'controller'       => 'Items',
        'action'           => 'List',
        'pattern'          => '/list',
    ],
    'detail'               => [
        // definice pomocí souhrnné vlastnosti `controllerAction`
        'controllerAction' => 'Items:Detail',
        'pattern'          => '/detail/<id>',
        'constraints'      => [
            'id'           => '\d+',
        ]
    ],
    // definice `controller:action` v definici názvu routy
    'Items:Edit'           => [
        'pattern'          => '/edit/<id>',
        'constraints'      => [
            'id'           => '\d+',
        ]
    ]
]);
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Pojmenování routy

Routy je dobré si nějak pojmenovat vlastností `name`. Tato vlastnost slouží jako první parametr 
při vytváření URL adres v aplikaci:

```php
// ./App/Bootstrap.php:
$router->AddRoutes([
    'list'   => [
        'controllerAction' => 'Items:List',
        'pattern'          => '/list',
    ],
    [
        'name'             => 'detail',
        'controllerAction' => 'Items:Detail',
        'pattern'          => '/detail/<id>',
        'constraints'      => [
            'id'           => '\d+',
        ]
    ]
]);

// ./App/Controllers/Items.php:
$detailUrl = $this->Url('detail', ['id' => 123]);
```

Název routy je možné uvést jako klíč v poli předávaném do metody `$router->AddRoutes()`
nebo explicitně v konfiguračním poli routy zadat vlastnost routy `name`.

Názvy rout jsou velmi praktické, neboť si vývojáři nemusí pamatovat vždy úplné cesty
ke všem controllerům nebo názvy všech akcí v PHP, kam chtějí URL odkázat.
Stačí, když si zapamatují název nebo pokud budou mít nějakou svoji jednoduchou pomůcku,
jak si název URL v aplikaci jednoduše odvodit.

Název routy je dobré mít vždy uveden především pro refactoring, kdy se často děje to, že se přesouvá
nebo přejmenovává celý controller nebo jeho akce. Poté je nutné se změněným názvem změnit jen routu, 
kde se změněný název controlleru nebo akce vyskytl. Není však třeba již procházet kód a hledat 
výskyty volání metody `$controller->Url()`, kde se může vyskytovat volání refactorovaného controlleru nebo akce.

Název routy může být libovolný string (vyjma systémových názvů níže). Obvykle se jedná o lowercase název, kde jsou slova
oddělena podtržítkem. Název routy zůstává pouze na serveru v routeru a nikdy není možné ho vidět od uživatele,
pokud tak vývojář explicitně neučiní.

Některé názvy rout jsou využívány systémem, proto prosím nepoužívejte ve svých názvech rout 
systémové názvy, které najdete v interface [**`\MvcCore\Router\IConstants`**](https://github.com/mvccore/mvccore/blob/master/src/MvcCore/Router/IConstants.php). Jinak byste je museli přepisovat jako konstanty na extendovaném routeru.

Systémové názvy rout jsou následující:
- `default` (`$router::DEFAULT_ROUTE_NAME`):
  - název routy vytvářené automaticky při routování pomocí controlleru a akce v query stringu,
- `not_found` (`$router::DEFAULT_ROUTE_NAME_NOT_FOUND`):
  - název routy vytvářené automaticky při chybové stránce nenalezeného zdroje (chyba 404),
- `error` (`$router::DEFAULT_ROUTE_NAME_ERROR`):
  - název routy vytvářené automaticky při obecné chybové stránce.


&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Parametry routy
Routa může mít svoje parametry. Nejedná se o query string parametry oddělené znakem ampersand `&`,
ale o parametry, které jsou součástí dotazované cesty v URL a kdy se z části zachycené cesty URL
vytvoří hodnota parametru routy, se kterou je možné později v aplikaci pracovat.

Parametry routy se definují nejprve ve vlastnosti `pattern` pomocí ostrých závorek jako `<nazev_parametru>`.
Pokud bych tedy chtěl v URL použít znaky `<` nebo `>`, je to kvůli syntaxy parametrů nevhodné, nikoliv nemožné (bylo by třeba regulární výraz definovat ručně).
Pokud chceme mít nějakou výchozí hodnotu tohoto parametru, můžeme ji v routě uvést ve vlastnosti `defaults`.
Parametry mohou být v definici routy povinné i nepovinné. Nepovinné parametry se označují ve vlastnosti routy
`pattern` hranatými závorkami. S nepovinnými parametry je možné označit i část přiléhající cesty u parametru:
```php
$router->AddRoutes([
    // parametr <name> je povinný, může obsahovat pouze znaky 
    // `a-zA-Z0-9_\.\-` a jeho výchozí hodnota je `first`:
    'detail' => [
        'controllerAction' => 'Items:Detail',
        'pattern'          => '/detail/<name>',
        'defaults'         => [
            'name'         => 'first',
        ],
        'constraints'      => [
            'name'         => '[a-zA-Z0-9_\.\-]+',
        ]
    ],
    // parametr <grid> je nepovinný (společně s lomítkem před sebou samým)
    // a pokud je uveden, může obsahovat cokoliv včetně dalších lomítek:
    'list'   => [
        'controllerAction' => 'Items:List',
        'pattern'          => '/list[/<grid>',
        'constraints'      => [
            'grid'         => '.*'
        ]
    ],
]);
```

Nepovinných sekcí s parametry může být v routě i více. Routa může obsahovat i nepovinnou sekci neobsahující parametr.
Routy jsou velmi flexibilní ve svém zápisu a navíc umožňují psát vlastní regulární výrazy.
Nejpohodlnější cesta jak psát vlastní regulární výrazy je nainstalovat si vývojářskou extenzi 
[**`mvccore/ext-debug-tracy-routing`**](https://github.com/mvccore/ext-debug-tracy-routing), která zobrazuje debugovací panel routeru s routami 
i v podobě regulárního výrazu. Tento automaticky vygenerovaný regulární výraz je možné si zkopírovat a upravit podle svého uvážení.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Výchozí hodnoty parametrů

Výchozí hodnoty parametrů routy je možné definovat ve vlastnosti `defaults`. Je třeba definovat asociativní pole,
kde klíč je název parametru a hodnota je výchozí hodnota parametru routy. Výchozí hodnoty jsou 
použity pro případ, že dotazovaná URL má nepovinný parametr a parametr v zachycené cestě z URL chybí nebo je prázný string.
Výchozí hodnota může být PHP typ `string`, `int`, `float`, `bool` nebo `NULL`, ale hodnota získaná z cesty 
z dotazované URL bude vždy v parametrech routy jako `string`.
K žádnému automatickému přetypování podle výchozí hodnoty v momentě zachycení routy podle URL neprobíhá.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Omezení parametrů

Omezení parametrů slouží pro to, aby nikdo nemohl do aplikace dopravit parametr s jinou hodnotou, než je omezení routy.
Omezení je možné definovat ve vlastnosti `constraints`. Je třeba definovat asociativní pole, kde klíč je název parametru 
a hodnota je část regulárního výrazu, který definuje, jaké znaky může mít parametr v regulárním výrazu.
Pokud tedy URL nevyhovuje omezení parametru v `constraints`, není routa ani zachycena podle dotazované URL.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

### Univerzální routy
Je možné vytvořit routu, která bude zachycovat nějaký nejčastěji používaný styl routování Vašich controllerů.
Nebo lze vytvořit routu, která bude zachycovat celou cestu v URL a podle ní získávat data např. z databáze.
Obojí je celkem časté a znamená to zařadit routu na poslední místo, kdy žádné speciálnější chování nevyhovuje
a kdy je tedy třeba použít nějaké univerzální.

**Univerzální MVC routa**
```php
$router->AddRoute([
    'name'        => 'mvc',
    'pattern'     => '/<controller>/<action>[/<id>]',
	'constraints' => [
        'id'      => '\d+'
    ]
]);
```

**Univezální "catch all" routa**
```php
$router->AddRoute([
    'name'        => 'docs',
    'controller'  => 'Documents',
    'pattern'     => '<path>',
    'constraints' => [
        'path'    => '.*'
    ]
]);
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Další features routeru
Router v jádře MVC nabízí několik dalších funkcí zmíněných níže 
a i několik malých, které jistě budou zřejmé z PHP Docs setter funkcí samotnéh routeru.

### Kanonické přesměrovávání
Router umí přesměrovávat více tvarů adres na kanonickou (tedy jedinečnou, primární) URL.
Při nalezení odpovídající routy zkouší router sestavit s routou a dotazovanými parametry
svoji aktuální URL adresu. Pokud je jiná než skutečně dotazovaná, přesměruje automaticky 
klienta na primární verzi URL adresy. Tuto feature je možné vypnout pomocí:
```php
$router->SetAutoCanonizeRequests(bool $autoCanonizeRequests): \MvcCore\Router;
```
...ale není to doporučované. Ve výchozím stavu je tato vlastnost zapnutá a router 
přesměrovává na kanonické URL adresy.

### Koncové lomítko v URL adresách
Router také umí chování pro koncové lomítko v URL:
```php
// router vždy přesměruje na verzi URL bez koncového lomítka (výchozí stav):
$router->SetTrailingSlashBehaviour(\MvcCore\IRouter::TRAILING_SLASH_REMOVE): \MvcCore\Router;

// router tvar koncového lomítka neřeší a na kanonickou verzi URL kvůli tomu nepřesměrovává:
$router->SetTrailingSlashBehaviour(\MvcCore\IRouter::TRAILING_SLASH_BENEVOLENT): \MvcCore\Router;

// router vždy přesměruje na verzi URL s koncovým lomítkem:
$router->SetTrailingSlashBehaviour(\MvcCore\IRouter::TRAILING_SLASH_ALWAYS): \MvcCore\Router;
```

### Dynamické načítání rout z databáze
Router si také umí načítat URL např. z databáze při zachycování routy podle URL i při sestavování routy na URL:
```php
$router->SetPreRouteMatchingHandler(
    function (\MvcCore\IRouter $router, \MvcCore\IRequest $request, $firstPathWord) {
        // load any routes from database:
        $routes = $db->loadRoutingRoutesGroup($firstPathWord);
        // add loaded routes into router:
        $router->AddRoutes($routes, $firstPathWord);
    }
);
$router->SetPreRouteUrlBuildingHandler(
    function (\MvcCore\IRouter $router, $controllerActionOrRouteName, array $params = []) {
	    // load any routes from database:
	    $routes = $db->loadUrlRoutesGroup($controllerActionOrRouteName);
	    // return routes in array with keys to be route name for each route:
	    return $routes;
	};
);
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Rozšířené routery

Routery jsou v MvcCore velmi propracované a rozšíření routerů umí věci, které určitě potěší.
Ve zkratce je možné nainstalovat si router, který řeší:
- lokalizaci aplikace,
- verzi aplikace pro mobily nebo počítače,
- více domén v aplikaci,
- vše výše uvedené rlzně kombinované.

Seznam rozšíření routeru MvcCore je k nalezení v sekci [**Seznam extenzí a repozitářů**](../../../extensions.md#rozšíření---routery).

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Ukládání rout do cache

Pro ukládání rout do cache je třeba mít nainstalovanou některou z cache extenzí, více v sekci [**Seznam extenzí a repozitářů**](../../../extensions.md#rozšíření---cache).
Před uložením jakékoliv routy do cache je třeba vše v inicializovat pomocí metody `$route->InitAll()`.
Routy tak lze pohodlně a kompletně inicializovat a uložit do cache a při dalším požadavku
je při inicializaci routeru dostat z cache a použít v rámci routeru:
```php
class Router extenre \MvcCore\Router {
    public function __constructor () {
        /** @var \MvcCore\Ext\ICache $cache */
        $cache = \MvcCore\Ext\Cache::GetStore(\MvcCore\Ext\Caches\Redis::class);
        $cacheKey = 'routes';
        $cacheEnabled = $cache->GetEnabled();
        /** @var $routesGroups ?array */
        $routesGroups = NULL;
        if ($cacheEnabled) 
            $routesGroups = $cache->Load('routes');
        if ($routesGroups !== NULL) {
            // set up routes from cache:
            $allRoutesByNames = [];
            foreach ($routesGroups as $routesGroup) {
                foreach ($routesGroup as $routeName => $routeInstance) {
                    $allRoutesByNames[$routeName] = $routeInstance;
                    $this->urlRoutes[$routeName] = $routeInstance;
                    $controllerAction = $routeInstance->GetControllerAction();
                    if ($controllerAction !== ':')
                        $this->urlRoutes[$controllerAction] = $routeInstance;
                }
            }
            $this->routes = $allRoutesByNames;
            $this->routesGroups = $routesGroups;
            $this->anyRoutesConfigured = TRUE;
        } else {
            // set up all routes into router from code:
            $this->setUpRoutesConfiguration();
            if ($cacheEnabled) {
                foreach ($this->routes as $routeInstance)
                    $routeInstance->InitAll();
                $cache->Save($cacheKey, $this->routesGroups, NULL, static::CACHE_TAGS);
            }
        }
    }
    protected function setUpRoutesConfiguration (): void {
        $this->AddRoutes([ /* prepare all routes here */]);
    }
}
```

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

<div class="prev-next">

[předchozí: **Průchod zpracování v aplikaci**](./app-dispatch.md)  
[další: **Vlastní handlery klíčových událostí**](./custom-handlers.md)  

</div>