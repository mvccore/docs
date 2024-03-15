# O frameworku

MvcCore framework vznikl v roce 2016 a vyvíjí se jako soubor knihoven pro následující účely:

## Vývoj malých aplikací
Vývoj malých nebo jednostránkových aplikací, kdy není žádoucí využívat 
jednu velikou knihovnu frameworku nebo příliš velký počet knihoven 
framworku rozděleného na menší balíčky. Avšak frameworky rozdělené na 
více balíčků nás stále nutí svými provázanostmi nakonec nainstalovat 
většinu knihoven, které se pak stejně používají i na velké projekty aplikace. 

Vývojář by si měl v základu vystačit pouze s jádrem nebo nějakým 
debugovacím nástrojem pro vývoj. Neměl by příliš řešit tooling na malých věcech.

## Vývoj středních a velkých aplikací
Vývoj středných a rozsáhlých projektů, kdy je možné MVC jádro frameworku rozšiřovat podle
potřeby aktuálního projektu a mít tak možnost napsat jakkoliv rozsáhlou aplikaci.

Framework si klade za cíl nedefinovat příliš velké provázanosti mezi svými balíčky.
Vývojář může pracovat tak, aby mohl vždy jakoukoliv celou supertřídu z jádra nahradit 
kompletně vlastním řešením a vlastní cestou. Tedy např. sestavit z traitů 
svoji vlastní singleton aplikaci s vlastními pravidly nebo sestavit z traitů nějakého 
rozšíření vlastní router, modelovou třídu, session, controller, formulář ... nebo cokoliv 
jiného a implementovat některé nebo většinu věcí vlastním směrem. Což je nynější 
princip práce všech extenzí MvcCore, které takto vždy rozšiřují jádro.

V MvcCore je vyřešeno z pohledu MVC mnoho obvyklých neduhů nebo chybějících 
implementací z frameworků. Například hlubší strukturování controllerů a views, 
komplexní routování pomocí extenzí, možnost strukturovat celky aplikací do Composer 
balíčků včetně šablon a TS/JS/CSS souborů s typovým provázáním, předvolené datagridy apod.

Framwork také obsahuje v rozšířeních precizně zpracované formuláře, 
autentizaci uživatelů, cachování, různé možnosti konfigurace, překladače, 
rozšíření pro debugování a mnoho dalšího.

## Rychlá křivka učení, žádné magické features
Framework si klade za cíl používat co nějméně magie, která brzí nové vývojáře 
v normální práci s framworkem dokud se nenaučí specifickou magii v MVC, nový 
šablonovací jazyk apod. 

Vývojář by si měl apliaci ve framworku otevřít v IDE, přečíst si základní dokumentaci
o vyřízení a obsluhu HTTP dotazu a začít bez obav s frameworkem pracovat na základě 
znalosí OOP v PHP.

## Neprogramovat "rovnáky a ohýbáky"
Mnoho frameworků nabízí pouze určité způsoby řešení problému a nuté jít vývojáře pouze
několika doporučenými cestami. Má to své důvody. Především nasměrovat začátečníky 
na nějaký styl práce, který jim zamezí psát špatně udržitelný kód, zamezí dělat 
bezpečnostní chyby a mnoho dalších. MvcCore framwork ale necílí na začátečníky nebo 
na vývojáře, kteří by potřebovali vychovávat. Cílí na lidi co mají zkušenost a praxi.
Na lidi, kteří potřebují běžně vytvářejí i neobvyklá řešení a nechtějí programovat 
rovnáky a ohýbáky a trávit čas nad tím, jak hacknout ramework aby to v něm šlo napsat.
MvcCore nabízí volnost a flexibilitu a do nesnaží se moc do ničeho nutit.
Nabízí způsoby jak udělat věci předvolenou cestou ale nezamezuje udělat to i naprosto po svém.

## Dlouhodobá podpora PHP
Framework nechce serfovat po přechodných módách a používat pouze super žhavé PHP features.
Podpora nové verze PHP je samozřejmostí. Ale MvcCore chce tako podporovat co nejvíce 
starší verze PHP, kvůli větší svobodě užití aplikací i na starších běhových platformách
bez nustnosti rekonfigurovat server, protože i taková je realita. 

Vývojář může svoji novou aplikaci samozřejmě psát v nejnovější syntaxy PHP, 
přesto je framwork a jeho příkaldy možné používat i na starších běhových PHP verzích.

## Vývoj jednosouborových aplikací a nástrojů
Vývoj jednosouborových aplikací v MVC, které bez tohoto frameworku trpí obvyklými 
neduhy jako nepřehlednost zdrojových kódů nebo zabalení do PHAR s pomalejší interpretací. 

Framework řeší tento problém tak, že vývojář píše aplikaci celkem obvyklou cesto 
vyjma assetů a posléze ji může zabalit do jednoho PHP nebo PHAR souboru se několika 
módy fungování vůči filesystému. Je možné se držet pouze souborů v balíčku nebo 
akceptovat soubory zvenčí či oboje s jinou prioritou.

Pomocí knihovny MvcCore `Packager` lze také zabalit plně cizí knihovny do jediného
PHP souboru, což zrychluje interpretaci PHP díky tomu, že se využívá maximálně 
jeden OPCODE z vyrovnávací paměti a neprobíhá ověžování a reinterpretace před 
seskládáváním jednotlivých objektů do konečného výsledu pro zpracování.
