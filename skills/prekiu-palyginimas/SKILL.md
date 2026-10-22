---
name: prekiu-palyginimas
description: Validuoja ir palygina iki 5 prekių modelių pagal patvirtintas kainas, normalizuotas specifikacijas ir vartotojo kriterijus; aptinka prieštaravimus ir perduoda struktūruotą JSON rekomendavimo etapui. Naudok po prekiu-paieska arba gavus modelių duomenis, kai prašoma palyginti prekes, privalumus ir kompromisus be galutinio laimėtojo.
---

# Prekių palyginimas (v2)

## Paskirtis
Antras etapas: `prekiu-paieska` → **`prekiu-palyginimas`** → `prekiu-rekomendacija`. Palygink iki 5 skirtingų modelių. **Neskirk bendrų balų, neišrink laimėtojo ir nenurodyk, ką pirkti.** Šis Skill nekeičia paieškos rezultatų be įrodymų.

## 1. Privalomas įvesties validavimas (prieš palyginimą)
1. Priimk ankstesnio `prekiu-paieska` JSON ar vartotojo duomenis. Jei nėra identifikuojamų modelių, paprašyk sąrašo / JSON. Jei vienas modelis — pateik santrauką, ne tarpusavio palyginimą.
2. Patikrink šakninę struktūrą: `category`, `budget_eur` (skaičius arba null), `country`, `requirements[]`, `preferences[]`, `search_date`, `products[]`. Išsaugok `checked_sellers` ir `search_limitations`, jei perduoti.
3. Kiekvienam produktui patikrink `id`, `brand`, `model`, `price_eur`, `seller`, `product_url`, `manufacturer_url`, `features[]`, `availability`, `verified_price`, `price_conditions`, `alternative_offers[]`, `unverified_fields[]`. Neprivalomus trūkstamus laukus normalizuok į `null` arba `[]`, pažymėk kaip nežinomus; nekurk naujų faktų.
4. Identifikuok dublikatus pagal gamintoją + tikslų modelio kodą (ne vien rinkodarinį pavadinimą). Jei neįmanoma atskirti modelių, pažymėk konfliktą; nevertink jų kaip dviejų tikrai skirtingų.
5. Patikrink kainos tipą, valiutą, `verified_price` loginį tipą ir biudžetą. `null` nėra 0. `verified_price: true` laikyk **ankstesnio etapo deklaracija**, ne nepriklausomu patikrinimu. Jei nėra šaltinio ar tikslus modelis neaiškus, pažymėk abejones.
6. Jei daugiau kaip 5 modeliai, prašyk pasirinkti arba aiškiai palygink tik pirmus 5, išsaugodamas pranešimą apie apribojimą. Jei nepakanka duomenų, tęsk dalinį palyginimą, o ne užpildyk spragas spėjimais.
7. Kiekvienam privalomam reikalavimui priskirk `atitinka`, `neatitinka` arba `nepatvirtinta`. Bendra `requirement_status` yra `neatitinka`, jei bent vienas aiškiai neatitinka; kitu atveju `nepatvirtinta`, jei bent vienas nepatvirtintas; kitu atveju `atitinka`. Biudžeto atitikimas reikalauja patvirtintos kainos.

## 2. Įrodymų ir prieštaravimų kontrolė
- Skirk `confirmed` (tiesioginis atitinkamas šaltinis), `reported` (perduotas, bet iš naujo nepatikrintas faktas), `unverified` (trūksta įrodymų), `conflicting` (nesutampa šaltiniai). Palyginimo metu neperrašyk `reported` į `confirmed` be naujos patikros.
- Specifikacijoms pirmenybę teik tikslaus modelio oficialiam gamintojo puslapiui ar dokumentacijai; kainoms — konkretaus pardavėjo prekės puslapiui. Gamintojo bendras kategorijos puslapis nepatvirtina kiekvieno modelio specifikacijų.
- Aptik kainų, modelio kodų, komplektacijų, matavimo sąlygų ir savybių prieštaravimus. Fiksuok `issues` su produkto ID, lauku, problema ir reikalinga patikra.
- Jei nurodytas `manufacturer_url` arba `product_url`, tai savaime **nereiškia**, kad nuoroda atverta ar patikrinta.
- Jei vartotojas prašo aktualių kainų, o paieškos duomenys seni / ginčytini, pagal galimybes atverk tiesioginius puslapius. Jei internetas nepasiekiamas, aiškiai pažymėk, kad naudojami ankstesnio etapo duomenys. Nepateik šio etapo kaip naujos paieškos.
- Nežinoma ≠ nėra. Nenumanyk ANC, baterijos, atsparumo ar suderinamumo iš modelio pavadinimo.

## 3. Specifikacijų normalizavimas
- Sukurk vienodą kiekvieno kriterijaus apibrėžimą ir matavimo kontekstą; originalią reikšmę išsaugok.
- Kainos: EUR, skirk įprastą kainą nuo kuponų, lojalumo ar kitų sąlygų; nepatvirtintų alternatyvių kainų nenaudok „pigiausios“ išvadai.
- Baterija: atskirk **vieno įkrovimo** ir **su dėklu** trukmę, ANC įjungta / išjungta, skambučiai / muzika, gamintojo deklaruota / išmatuota. Jei sąlygos skiriasi arba nežinomos, reikšmes rodyk su paaiškinimu, **neskaičiuok tiesioginio pranašumo**.
- Talpa: konvertuok tik suderinamus vienetus (pvz., GB ↔ TB su aiškia baze), nekeičiant reikšmės; atsparumo standartų, ekranų matų ar našumo rodiklių nevadink lygiaverčiais be pagrindo.
- Jei negali normalizuoti, nustatyk `comparable: false`, `normalized_value: null`, nurodyk priežastį; jokio spėjimo.

## 4. Dinaminiai kriterijai
Visada įtrauk `price_eur`, `requirements`, `availability`, `evidence_quality`. Pridėk **3–5 kategorijai aktualius kriterijus**, pirmiau vartotojo prioritetus, tik jei yra bent dalis pagrįstų duomenų:
- Ausinės: konstrukcija / prigludimas, ANC, baterija su sąlygomis, atsparumas, ryšys.
- Telefonai: ekranas, atmintis, baterija, kameros, programinės įrangos palaikymas.
- Kavos aparatai: aparato tipas, gėrimų pasirinkimas, priežiūra, talpa, matmenys.
- Kitoms kategorijoms kriterijus išvesk iš vartotojo poreikių ir realių specifikacijų; nenaudok universalaus sąrašo per prievartą.
- Nepridėk neprašytų privalomų kriterijų. Jei informacijos nepakanka, palik `null` ir paaiškink.

## 5. Palyginimas ir atsakymas
1. Pateik kategoriją, biudžetą, paieškos ir palyginimo datas, patikrų apimtį.
2. Lentelė: **kriterijai eilutėse, modeliai stulpeliuose**. Kiekvieną nepatikrintą faktą pažymėk, nenurodyk `nėra` vietoje `nežinoma`.
3. Kiekvienam modeliui 1–3 faktiniais duomenimis pagrįsti privalumai ir kompromisai. Galima įvardyti objektyvius kainų skirtumus tik tarp palyginamų patvirtintų kainų.
4. Atskirai pateik neatitikimų / nepatvirtintų duomenų registrą ir ką reikia patikrinti prieš rekomendaciją.
5. Jokio galutinio reitingo ar vieno laimėtojo.

## 6. Kompaktiškas JSON perdavimas
Po lentelės pateik **validų JSON kodų bloke** kitam Skill. Kad nedubliuotum viso produkto duomenų, laikyk pirminius modelius `products` masyve, o analizę — atskirame `comparison` objekte, susietame per `id`. **Išsaugok visus gautus produktų ir pasiūlymų laukus bei URL, net jei schema pavyzdyje sutrumpinta.** Nesugeneruok naujų modelių ar nuorodų.

```json
{
  "schema_version": "2.0",
  "category": "belaidės ausinės",
  "budget_eur": 300,
  "country": "Lietuva",
  "requirements": [],
  "preferences": [],
  "search_date": "YYYY-MM-DD",
  "comparison_date": "YYYY-MM-DD",
  "checked_sellers": [],
  "search_limitations": [],
  "products": [
    {
      "id": 1,
      "brand": "Gamintojas",
      "model": "Modelis",
      "price_eur": null,
      "seller": null,
      "product_url": null,
      "manufacturer_url": null,
      "features": [],
      "availability": "nežinoma",
      "verified_price": false,
      "price_conditions": null,
      "alternative_offers": [],
      "unverified_fields": []
    }
  ],
  "comparison": {
    "criteria": ["price_eur", "requirements", "availability", "evidence_quality", "ANC"],
    "definitions": {
      "ANC": "Aktyvus triukšmo slopinimas: taip / ne / nežinoma"
    },
    "by_product_id": {
      "1": {
        "requirement_status": "nepatvirtinta",
        "criteria_values": {
          "price_eur": null,
          "requirements": "nepatvirtinta",
          "availability": "nežinoma",
          "evidence_quality": "unverified",
          "ANC": null
        },
        "normalized_metrics": {},
        "advantages": [],
        "tradeoffs": [],
        "notes": []
      }
    },
    "issues": [
      {
        "product_id": 1,
        "field": "ANC",
        "status": "unverified",
        "detail": "Nėra patvirtinto šaltinio",
        "next_check": "Patikrinti tikslaus modelio gamintojo specifikaciją"
      }
    ],
    "limitations": [],
    "winner_selected": false
  }
}
```

Tai **tik schemos pavyzdys**. `criteria_values` raktai turi tiksliai sutapti su `comparison.criteria`. `normalized_metrics` reikšmėms naudok objektus su `raw_value`, `normalized_value`, `unit`, `conditions`, `comparable`, `evidence_status`, kai metrika aktuali. Jei duomenų nėra, naudok `null`, ne 0. `issues` laikyk konkrečius trūkumus, ne bendrus spėjimus.

**Suderinamumas:** `prekiu-paieska` v2 įvestis lieka nepakitusi; `prekiu-rekomendacija` turės priimti naują `schema_version: "2.0"` ir skaityti `comparison.by_product_id`, o ne senus `products[].criteria_values`. Kol trečias Skill nesukurtas, neskubėk jo laikyti suderintu.

## 7. Privaloma patikra prieš atsakymą
- Iki 5 unikalių modelių; nėra išgalvotų faktų ir dublių.
- Neperrašytos originalios nuorodos, kainų patikros žymos ir alternatyvūs pasiūlymai.
- Aiškiai atskirtas šaltinio patikimumas, pasenę duomenys ir prieštaravimai.
- Normalizavimo sąlygos sutampa arba reikšmės pažymėtos nepalyginamomis.
- Visi privalomi kriterijai turi teisingą `requirement_status`.
- JSON sintaksiškai validus; visi `by_product_id` raktai atitinka `products[].id`, o kriterijų raktai sutampa.
- `winner_selected` yra `false`, nėra bendro balo ar galutinės rekomendacijos.

Testavimo atvejai: [evals/evals.json](evals/evals.json).
