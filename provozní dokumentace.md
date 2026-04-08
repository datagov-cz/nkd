# Provozní dokumentace Národního katalogu otevřených dat

## Seznam pojmů a zkratek

<dl>
    <dt>DCAT-AP-CZ</dt>
    <dd>Otevřená formální norma "DCAT-AP-CZ: Rozhraní katalogů otevřených dat" založená na evropském standardu DCAT-AP, který je založen na webovém standardu DCAT</dd>
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

## Popis funkce systému a jednotlivých modulů

Viz [aplikační dokumentace](aplikační%20dokumentace.md).

## Nasazení systému
Instalace systému viz [instalační dokumentace](instalační%20dokumentace.md).

NKOD je nasazen ve 3 instancích, produkční, testovací a vývojové, v prostředí Microsoft Azure a Azure Kubernetes Service.
Škálován je na práci s 50 000 datovými sadami.

### Produkční prostředí
Nastavení prostředí viz https://github.com/datagov-cz/nkod-nasazeni/tree/develop/azure-kubernetes-service.
Běží na adrese https://data.gov.cz, přijímá registrace z datové schránky `m3hp53v` a případné testovací záznamy z `main` branche https://github.com/datagov-cz/nkod-test.
Nastaveno na harvestaci denně, v 22:00.

Kromě frontendu NKOD zde běží také POD, který je klonem stránek spravovaných v `main` branchi https://github.com/datagov-cz/data.gov.cz, a dále hostuje otevřené formální normy z `master` branche https://github.com/datagov-cz/otevrene-formalni-normy na https://ofn.gov.cz.
Obě další části se stahují na základě obsluhy GitHub webhooku.

### Testovací prostředí
Nastavení prostředí viz https://github.com/datagov-cz/nkod-nasazeni/tree/develop/azure-kubernetes-service.
Běží na adrese https://pod-test.dia.gov.cz, přijímá registrace z **testovací** datové schránky `vrxgfvc` a testovací záznamy z `test` branche https://github.com/datagov-cz/nkod-test.
Nastaveno na harvestaci každé 3 hodiny.

Kromě frontendu NKOD zde běží také pracovní verze POD, která je klonem stránek spravovaných v `develop` branchi https://github.com/datagov-cz/data.gov.cz, a dále hostuje otevřené formální normy z `develop` branche https://github.com/datagov-cz/otevrene-formalni-normy na https://pod-test.dia.gov.cz/otevřené-formální-normy/, např. https://pod-test.dia.gov.cz/otevřené-formální-normy/základní-datové-typy/2020-07-01/ .
Obě další části se stahují na základě obsluhy GitHub webhooku.

## Údržba systému
Je třeba monitorovat a upravovat historii měření datové kvality, která je udržována ve SPARQL endpointu. Doporučená velikost historie je 6 měsíců. Po překročení této velikosti může docházet ke zpomalení či výpadkům SPARQL endpointu. Regulace se provádí přesunem starších záznamů na úložišti `nkd-public-pvc`, např. skrz pod `nkd-public-data-deployment` v cestě `/usr/share/nginx/html/soubor/kvalita` do podadresáře `offline`, kde jsou dlouhodobě uložena. Jedná se o soubory s datumem, konkrétně `dostupnost-YYYY_MM_DD.ttl.gz`, `dostupnost-cors-YYYY_MM_DD.ttl.gz`, `kvalita-YYYY_MM_DD.ttl.gz` a `kombinované-indikátory-YYYY_MM_DD.ttl.gz`.

## Administrátorský přístup k LinkedPipes ETL
[LinkedPipes ETL], ve kterém běží hlavní datové procesy NKOD má uživatelské rozhraní přístupné viz https://github.com/datagov-cz/nkod-nasazeni/tree/develop/azure-kubernetes-service#přístup-k-linkedpipes-etl.

## Možné chybové stavy a jejich řešení
Může proces harvestace NKOD selhat z následujících očekávatelných důvodů:
1. Selže pipeline `07.1 Harvestace LKOD a formulářů, aktualizace uživatelského rozhraní`, protože systém datových schránek má výpadek. Ten obvykle trvá jeden den, tedy ten den nebude NKOD harvestován. Není nutný další zásah.

## Nároky na personál

Na provozovatele NKOD jsou kladeny následující nároky:
- Administrace Linuxových systémů a Kubernetes
    - cron
    - bash
    - docker
    - Kubernetes
- Uživatelská znalost Microsoft Azure prostředí pro virtualizaci
- Konfigurace sítí TCP/IP
- Konfigurace HTTP(S) webových serverů (nginx) včetně správy SSL certifikátů
- GitHub
- PHP (použito v obsluze GitHub webhooků)
- Administrátorská znalost [LinkedPipes ETL], [Katalog], [LinkedPipes DCAT-AP Forms], [NKOD-ISDS], [NKOD-MS], [OpenLink Virtuoso Open-Source], [GraphQL server NKOD]
- Uživatelská znalost [LinkedPipes ETL]

[Katalog]: https://github.com/datagov-cz/katalog "Katalog záznamů, aplikací a návrhů"
[NKOD-MS]: https://github.com/datagov-cz/ms-adapter "Adaptér na MS Sharepoint"
[LinkedPipes DCAT-AP Forms]: https://github.com/datagov-cz/dcat-ap-forms "LinkedPipes DCAT-AP Forms"
[LinkedPipes ETL]: https://github.com/datagov-cz/etl "LinkedPipes ETL"
[NKOD-ISDS]: https://github.com/datagov-cz/nkod-isds "NKOD-ISDS"
[OpenLink Virtuoso Open-Source]: https://github.com/datagov-cz/virtuoso-opensource "OpenLink Virtuoso Open-Source"
[Linked Data Fragments server]: https://github.com/datagov-cz/Server.js "Linked Data Fragments server"
[GraphQL server NKOD]: https://github.com/datagov-cz/nkod-graphql "GraphQL server NKOD"
[POD]: https://data.gov.cz "Portál otevřených dat"
[Oficiální portál evropských dat]: https://data.europa.eu "Oficiální portál evropských dat"
[SPARQL endpoint]: https://data.gov.cz/sparql "SPARQL endpoint NKOD"
[National Single Information Point (NSIP)]: https://data.europa.eu/data/datasets?superCatalogue=erpd&locale=cs&catalog=nsip-cz&page=1 "Czech National Single Information Point"