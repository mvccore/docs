# Controllery a sub-controllery

## Obsah
- [**Úvod**](#úvod)
- [**Sub-controllery**](#sub-controllery)
- [**Obsah sekce**](#obsah-sekce)

## Úvod
Následující sekce se věnuje obvyklým konstrukcím s controllery.
Controllery jsme zatím znali jako třídy, které se starají o správné vyřízení 
požadavku, o svoje modely a proměnné potřebné pro vykreslení view šablon.

Controllery v MvcCore jsou třídy, které se samozřejmě mohou věnovat 
této základní činnosti vyřízení pořadavku podle routovaných akcí,
ale zároveň jsou využívány jako pod-třídy (sub-controllery) těchto 
hlavních controllerů odpovědných vyřízení routovaného požadavku.

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Sub-controllery
Primární účel sub-controllerů je oddělit určitou problematiku od
hlavního controlleru, který může být stručnější. Sub-controller si často
můžeme představit jako komponentu z jiných frameworků nazývaných jako
`control`, `widget` apod. V MvcCore je to stále instance třídy 
`\MvcCore\Controller` se stejnými pravidly. Jen pro lepší orientaci 
v hierarchii controllerů budeme tyto controllery nazývat `sub-controller`y.

Do sub-controllerů je možné v určité míře i navést tok programu 
vyřizování požadavku, ale nejde o primární účel sub-controllerů.
Subcontroller rovněž může ale i nemusí mít výstup renderování.
Primární účel může být např.:
- kompletace dat a rendering novinek na homepage,
- kompletace dat a rendering navigace,
- management formuláře v postranním widgetu stránky,
- třída formuláře (formulář je také sub-controller, jen specializovaný),
- třída datagridu (datagrid je také specializovaný subcontroller),
- globální management assetů pro velké projekty (management pro JS a CSS soubory),
- ...

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

## Obsah sekce
- [**Vytvoření a renderování sub-controllerů**](./sub-controllers-dispatching.md)
- [**Routování do sub-controllerů**](./sub-controllers-routing.md)
- [**Dočasné zprávy - flash messages**](./constructions/flash-messages.md)

&nbsp;  
[↑ Obsah](#obsah)  
&nbsp;&nbsp; 

---

[▲ o úroveň výš](../README.md)

<div class="prev-next">

[◀ předchozí: **Vytváření URL adres**](../urls/README.md)  
[▶ další: **Vytvoření a renderování sub-controllerů**](./sub-controllers-dispatching.md) 

</div>