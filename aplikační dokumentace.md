# Aplikační příručka Národního katalogu otevřených dat

## Seznam pojmů a zkratek

<dl>
    <dt>CORS</dt>
    <dd>Cross-Origin Resource Sharing</dd>
    <dt>DCAT-AP-CZ</dt>
    <dd>Otevřená formální norma "DCAT-AP-CZ: Rozhraní katalogů otevřených dat" založená na evropském standardu DCAT-AP, který je založen na webovém standardu DCAT</dd>
    <dt>DGA</dt>
    <dd>Data Governance Act - <a href="https://eur-lex.europa.eu/legal-content/CS/TXT/HTML/?uri=CELEX:32022R0868">Akt o správě dat</a></dd>
    <dt>ISDS</dt>
    <dd>Informační systém datových schránek</dd>
    <dt>LKOD</dt>
    <dd>Lokální katalog otevřených dat</dd>
    <dt>NKOD</dt>
    <dd>Národní katalog otevřených dat</dd>
    <dt>POD</dt>
    <dd>Portál otevřených dat</dd>
    <dt>RDF</dt>
    <dd>Resource Description Framework - datový model využívaný NKOD</dd>
    <dt>SPARQL</dt>
    <dd>Dotazovací jazyk nad daty v RDF</dd>
</dl>

## Popis funkcionality
Národní katalog otevřených dat (NKOD) obsahuje zejména databázi metadatových záznamů datových sad otevřených dat poskytovaných různými institucemi veřejné správy, která poskytuje [SPARQL endpoint] pro dotazování.
V databázi se zrcadlí metadataové záznamy datových sad registrovaných jednotlivě přímo v NKOD a záznamy pocházející z Lokálních katalogů otevřených dat (LKOD) provozovaných přímo poskytovateli dat.
Metadatové záznamy odpovídají specifikaci [DCAT-AP-CZ: Rozhraní katalogů otevřených dat]. 
Databáze NKOD je tvořena pravidelně denně.
Po každém vytvoření databáze NKOD je zhodnocena i kvalita metadatových záznamů vzhledem k DCAT-AP-CZ a dostupnost registrovaných zdrojů.
Na základě naměřených hodnot jsou vygenerovány reporty obsahující zjištěné skutečnosti.

Kromě datových sad otevřených dat NKOD obsahuje i datové sady Inventárního seznamu, což je seznam datových sad, ke kterým je třeba žádat o přístup dle DGA.
Datové sady inventárního seznamu jsou přítomny ve [SPARQL Endpoint], nejsou však viditelné v uživatelském rozhraní.
Slouží primárně pro harvestaci do [National Single Information Point (NSIP)] v rámci "European Register for Protected Data held by the Public Sector".

Kromě záznamů o datových sadách obsahuje NKOD i záznamy o aplikacích využívajících registrované datové sady a záznamy o požadavcích na datové sady k otevření.

Na vstupu tedy NKOD zajišťuje zpracování příchozích registrací z ISDS, následnou harvestaci metadat z LKOD, včetně registrace metadat přímo do NKOD, a také zpracovává registrace aplikací pracujících s otevřenými daty a požadavků na data k otevření.
Na základě těchto vstupů pak pravidelně nahránvá obsah NKOD do RDF databáze přístupné přes SPARQL endpoint, dále do Triple Pattern Fragments endpointu, GraphQL endpointu, vystavuje obsah NKOD v podobě souborů na webu, a nakonec také naplňuje databázi a indexy frontendové aplikace pro lidské uživatele, která obsahuje i registrační formuláře pro tvorbu registračních záznamů zasílaných pomocí ISDS.

NKOD je integrován do POD - portálu otevřených dat https://data.gov.cz, který také obsahuje informace pro poskytovatele a uživatele otevřených dat, školení a otevřené formální normy.
Tato dokumentace se dále zabývá pouze částí NKOD.

## Přehled komponent
Národní katalog otevřených dat se skládá ze 7 propojených hlavních částí:
1. Prohlížeč datových sad, registrovaných aplikací a návrhů sad k otevření ([Katalog])
2. Zadávací formuláře pro registraci datových sad a lokálních katalogů ([LinkedPipes DCAT-AP Forms])
3. Část zpracování dat z formulářů a harvestace lokálních katalogů ([LinkedPipes ETL])
4. Vyzvedávač datových zpráv z ISDS ([NKOD-ISDS])
5. Vyzvedávač seznamů registrovaných aplikací, návrhů dat k otevření a povolených poskytovatelů ze Sharepoint ([NKOD-MS])
6. Databáze pro dotazování nad metadaty a poskytování metadat Oficiálnímu portálu evropských dat ([OpenLink Virtuoso Open-Source])
7. [Linked Data Fragments server]
8. [GraphQL server NKOD]

NKOD očekává, že jednotlivé harvestované lokální katalogy otevřených dat (LKODy) dodržují Otevřenou formální normu [DCAT-AP-CZ: Rozhraní katalogů otevřených dat].
Na [Portálu otevřených dat][POD] je dále popsána [Správa záznamů lokálních katalogů](https://data.gov.cz/pro-poskytovatele/otevřená-data/katalogizace-dat/#katalogizace-pomocí-lokálního-katalogu) a [Správa záznamů jednotlivých datových sad](https://data.gov.cz/pro-poskytovatele/otevřená-data/katalogizace-dat/#přímá-katalogizace-datových-sad).

## Pravidelný běh NKOD
Pravidelný běh NKOD je zajištěn zejména pravidelně spouštěnými [datovými procesy (pipelinami)](pipeliny/README.md) implementovanými v nástroji [LinkedPipes ETL].

## Datový model
Data NKOD jsou uchovávána v datovém modelu [Resource Description Framework (RDF)] a skládají se z metadat datových sad registrovaných do NKOD či harvestovaných z LKOD.
Jejich struktura se řídí specifikací [DCAT-AP-CZ].

Část týkající se měření kvality záznamů pak vychází ze slovníku [Data Quality Vocabulary].

![Datový model pro měření kvality](diagramy/DQV.svg)

Kromě výše uvedených dat obsahuje SPARQL endpoint NKOD záznamy o registrovaných požadavcích a aplikacích používajících otevřená data.
Tyto záznamy se taktéž řídí [DCAT-AP-CZ] v případě požadavků, které jsou reprezentovány jako datové sady bez distribucí, a DCAT-AP pro aplikace, které jsou reprezentované jako `dcat:Resource` s [rozšířením definovaným v Sémantickém slovníku pojmů](https://slovník.gov.cz/prohlížíme/pojem?iri=https://slovník.gov.cz/legislativní/sbírka/106/1999/pojem/produkt-či-služba-využívající-otevřená-data).
Lepší představu o jejich reprezentaci poskytnou příslušné [pipeline](pipeliny/README.md) `07.3` a `07.4`.

# Nároky na uživatele
Uživatelé NKOD jsou následujících druhů.
<dl>
  <dt>Běžný návštěvník</dt>
  <dd>Používá hlavně frontendovou aplikaci pro procházení registrovaných datových sad, aplikací a požadavků</dd>
  <dt>Návštěvník se znalostí SPARQL</dt>
  <dd>Pro pokročilé dotazování a strojový přístup k obsahu NKOD je třeba znát SPARQL a RDF, znát specifikaci DCAT-AP-CZ, a pro zpracovávání údajů z měření kvality pak ještě Data Quality Vocabulary.</dd>
  <dt>Externí IS</dt>
  <dd>Externí IS bude přistupovat k webové službě SPARQL endpointu, GraphQL, Triple Pattern Fragments a nebo stahovat soubory s obsahem NKOD.
  Potřebuje knihovnu pro práci se SPARQLem, znát RDF, znát specifikaci DCAT-AP-CZ, a pro zpracovávání údajů z měření kvality pak ještě Data Quality Vocabulary.</dd>
  <dt>Poskytovatel otevřených dat</dt>
  <dd>
    Poskytovatel otevřených dat potřebuje využívat zejména funkcionalitu registračních formulářů pro tvorbu registračních záznamu k zaslání pomocí ISDS.
    Dále potřebuje kontrolovat své datové sady v NKOD z hlediska přítomnosti a kvality, což může dělat buď v uživatelském rozhraní frontendové aplikace, nebo skrze strojově čitelná rozhraní, pro která pak potřebuje znát RDF a SPARQL, znát specifikaci DCAT-AP-CZ, a pro zpracovávání údajů z měření kvality pak ještě Data Quality Vocabulary.
  </dd>
</dl>

[Katalog]: https://github.com/datagov-cz/katalog "Katalog - záznamy, aplikace, návrhy"
[LinkedPipes DCAT-AP Forms]: https://github.com/datagov-cz/nkd-formulare "LinkedPipes DCAT-AP Forms"
[LinkedPipes ETL]: https://github.com/datagov-cz/etl "LinkedPipes ETL"
[NKOD-ISDS]: https://github.com/datagov-cz/nkod-isds "NKOD-ISDS"
[NKOD-MS]: https://github.com/datagov-cz/ms-adapter "NKOD adaptér na Sharepoint"
[OpenLink Virtuoso Open-Source]: https://github.com/datagov-cz/virtuoso-opensource "OpenLink Virtuoso Open-Source"
[Linked Data Fragments server]: https://github.com/datagov-cz/Server.js "Linked Data Fragments server"
[GraphQL server NKOD]: https://github.com/datagov-cz/nkod-graphql "GraphQL server NKOD"
[DCAT-AP-CZ: Rozhraní katalogů otevřených dat]: https://ofn.gov.cz/dcat-ap-cz-rozhraní-katalogů-otevřených-dat/2024-05-28/ "DCAT-AP-CZ: Rozhraní katalogů otevřených dat"
[DCAT-AP-CZ]: https://ofn.gov.cz/dcat-ap-cz-rozhraní-katalogů-otevřených-dat/2024-05-28/ "DCAT-AP-CZ: Rozhraní katalogů otevřených dat"
[POD]: https://data.gov.cz "Portál otevřených dat"
[Oficiální portál evropských dat]: https://data.europa.eu "Oficiální portál evropských dat"
[SPARQL endpoint]: https://data.gov.cz/sparql "SPARQL endpoint NKOD"
[National Single Information Point (NSIP)]: https://data.europa.eu/data/datasets?superCatalogue=erpd&locale=cs&catalog=nsip-cz&page=1 "Czech National Single Information Point"
[Resource Description Framework (RDF)]: https://www.w3.org/TR/rdf11-concepts/ "RDF"
[Data Quality Vocabulary]: https://www.w3.org/TR/vocab-dqv/ "Data Quality Vocabulary"