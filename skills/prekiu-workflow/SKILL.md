---
name: prekiu-workflow
description: Viena nuosekli prekių pirkimo darbo eiga, kuri iš GitHub perskaito ir įvykdo prekiu-paieska, prekiu-palyginimas ir prekiu-rekomendacija Skills šia tvarka, perduodama patikrintus JSON duomenis tarp etapų. Naudok, kai vartotojas prašo surasti, palyginti ir rekomenduoti prekę vienu iškvietimu.
---

# Prekių workflow (v1)

## Tikslas
Vienu iškvietimu vykdyk tris atskirus, jau egzistuojančius Skills griežta tvarka:
1. `skills/prekiu-paieska/SKILL.md` – prekių paieška v2.
2. `skills/prekiu-palyginimas/SKILL.md` – palyginimas v2.
3. `skills/prekiu-rekomendacija/SKILL.md` – rekomendacija v1.

Šis Skill yra **orkestratorius**, o ne trijų instrukcijų kopija. Visų etapų taisyklės lieka jų pirminiuose failuose. Nekurk atskirų agentų, kurie dirba be priklausomybių, ir nevykdyk etapų lygiagrečiai.

## 1. Instrukcijų užkrovimas
- Pirmiausia atverk **naujausias** tris instrukcijas iš tos pačios GitHub repozitorijos `evelinapal/testing-skills` numatytoje šakoje. Naudok GitHub prieigos įrankį, jei pasiekiamas; negalima laikyti ankstesnės pokalbio santraukos ar failų pavadinimų pačiomis instrukcijomis.
- Jei GitHub nepasiekiamas arba kurio nors failo perskaityti nepavyksta, **neapsimesk**, kad jį vykdei. Paaiškink ribą ir paprašyk pateikti failą arba atkurti prieigą. Nevykdyk neperskaityto etapo pagal spėjimą.
- Patikrink, kad gauti `name:` būtų `prekiu-paieska`, `prekiu-palyginimas`, `prekiu-rekomendacija`. Taikyk jų naujausias taisykles; jei šio orkestratoriaus ir etapo taisyklės skiriasi, etapo duomenų kokybės taisyklės yra griežtesnės.

## 2. Vartotojo poreikių surinkimas
- Iš vartotojo užklausos išskirk kategoriją, biudžetą, šalį, privalomus reikalavimus ir pageidavimus. Jei vartotojas nenurodo prioritetų, **neišgalvok jų ir neskirk savavališkų svorių**.
- Jei trūksta kategorijos ar esminio biudžeto, užduok trumpą klausimą prieš paiešką; kitu atveju tęsk be nereikalingų patvirtinimų.
- Jei vartotojas prašo naudoti anksčiau gautą rezultatą, pakartotinai naudok tik realiai turimą, tinkamos versijos JSON; jei reikia naujų kainų, atlik naują patikrą.

## 3. Etapas A: prekiu-paieska
- Vykdyk visas perskaityto `prekiu-paieska` Skill taisykles: pardavėjų ir gamintojo šaltiniai, modelių identifikavimas, kainų sąlygos, iki 5 modelių ir pradinė JSON struktūra.
- Išsaugok **visą** pradinį JSON kaip `search_result`. Neišgalvok modelių, šaltinių ar sandėlio likučių.
- Vartai A→B: JSON turi turėti `products[]` su unikaliais `id`, `category`, `budget_eur`, `country`, `requirements[]`, `preferences[]`, `search_date`. Jei trūksta dalies laukų, papildyk tik `null` arba tuščiu masyvu ir užregistruok spragas. Jei nerasta nė vieno identifikuojamo modelio, stabdyk ir paaiškink paieškos ribas.

## 4. Etapas B: prekiu-palyginimas
- Perduok `search_result` **be nuostolių** perskaitytam `prekiu-palyginimas` v2 etapui. Vykdyk jo validavimą, dublikatų kontrolę, įrodymų būsenas, matavimo normalizavimą, kategorijos kriterijus ir `issues` registrą.
- Išsaugok rezultatą kaip `comparison_result`, kuriame `schema_version: "2.0"` ir `comparison.by_product_id` susietas per `String(product.id)`. Originalūs `products[]` ir nuorodos neturi dingti.
- Vartai B→C: patikrink, kad kiekvienas `products[].id` turi tiksliai vieną `comparison.by_product_id` įrašą; `comparison.criteria` atitinka `criteria_values` raktus; `issues` ir `limitations` neprarasti.
- Jei B negali patikimai identifikuoti modelių, neskubėk jų rekomenduoti: pažymėk `unverified` / `conflicting` ir perduok šiuos įspėjimus C. Jei schema nevalidi ir neįmanoma jos pataisyti nekeičiant faktų, sustabdyk ir pranešk klaidą.

## 5. Etapas C: prekiu-rekomendacija
- Perduok **tikrą** `comparison_result` (ne sugalvotą santrauką) perskaitytam `prekiu-rekomendacija` etapui. Taikyk privalomų reikalavimų filtrą, įrodymų patikimumo ir aktualumo kontrolę, vartotojo prioritetus bei kritinius `issues`.
- Jei duomenų pakanka, grąžink `recommended`; jei galima tik sąlyginė išvada – `conditional`; jei net sąlyginei išvadai nepakanka – `insufficient_evidence` su `recommended_product_id: null`.
- Nepriskirk `confirmed` statuso be tiesioginės šaltinio patikros; nepateik nepatvirtintos nuolaidos kaip pigiausio pasiūlymo. Kai palyginime `normalized_metrics.comparable: false`, nedaryk skaitinio pranašumo išvados.
- Jei C naujai patikrinti šaltiniai paneigia ar iš esmės pakeičia B faktus (pvz., tikslų modelio kodą, ANC, kainą), **atnaujink paieškos / palyginimo duomenis ir pakartok paveiktą palyginimą prieš galutinę rekomendaciją**. Negalima viename atsakyme naudoti prieštaraujančių versijų.

## 6. Rezultato formatas
Pirmiausia pateik aiškų vartotojui rezultatą:
1. Užklausos santrauką ir patikrų datą.
2. Trumpą 3 etapų vykdymo būseną (`completed`, `partial`, `blocked`).
3. Kompaktišką iki 5 modelių palyginimo lentelę (kaina, svarbiausi kriterijai, duomenų patikimumas).
4. Pagrindinę rekomendaciją arba `insufficient_evidence`, iki 2 alternatyvų, argumentus, kompromisus ir ką patikrinti.
5. Aiškiai atskirk **oficialų gamintojo šaltinį**, **pardavėjo puslapį** ir **trečiosios šalies apžvalgą**; nurodyk patikros ribas.

Po santraukos pateik **tris atskirus validžius JSON kodų blokus**, kad būtų galima testuoti perdavimą:
- `search_result`: visas A etapo JSON pagal paieškos Skill;
- `comparison_result`: visas B etapo `schema_version: "2.0"` JSON;
- `recommendation_result`: C etapo `schema_version: "1.0"` JSON su `source_comparison_version: "2.0"`.

Jei vartotojas prašo tik glausto rezultato, galima rodyti vieną galutinį JSON ir pateikti tarpinius pagal prašymą, **bet viduje visi trys etapai ir jų vartai vis tiek privalomi**.

## 7. Klaidos, saugikliai ir ribos
- Kiekvienas etapas turi įvykti po ankstesnio. Niekada nevadink `workflow` užbaigtu, jei praleidai vieną iš etapų.
- Nėra rezultatų A → stabdyk. Neteisinga schema B → stabdyk. Nepakankami įrodymai C → grąžink `insufficient_evidence`, o ne sugalvotą laimėtoją.
- Jei nėra interneto ar prieigos prie konkrečių šaltinių, nurodyk, kas patikrinta, o kas tik perkelta iš ankstesnės paieškos; jokio netikro „šiandien patvirtinta“.
- Nedubliuok produktų; neišgalvok URL, kainų, nuolaidų, modelių kodų, funkcijų ar naudotojo prioritetų.
- Niekada neatlik pirkimo ar užsakymo be aiškaus vartotojo prašymo ir atitinkamos autorizuotos funkcijos.
- Prieš atsakymą patikrink tris JSON sintakses, ID vientisumą, išsaugotas nuorodas, kritinių problemų perdavimą, sprendimo būseną ir šaltinių statusus.

Testavimo scenarijai: [evals/evals.json](evals/evals.json).
