---
name: prekiu-palyginimas
description: Palygina iki 5 prekių pagal kainą, specifikacijas, privalomus ir pageidaujamus kriterijus; naudoja prekiu-paieska JSON arba vartotojo pateiktus modelius. Naudok, kai prašoma palyginti prekes, nustatyti jų skirtumus, privalumus, trūkumus ar kainos ir savybių santykį, prieš galutinę prekiu-rekomendacija.
---

# Prekių palyginimas

## Paskirtis ir ribos
Tai **antras apsipirkimo darbo eigos etapas** po `prekiu-paieska`. Palygink iki 5 **skirtingų modelių**, aiškiai parodyk skirtumus ir duomenų patikimumą. **Nerink vieno galutinio laimėtojo, nesuteik bendro balo ir nenurodyk „pirk šį“** — tai `prekiu-rekomendacija` etapas. Jei vartotojas aiškiai prašo vien rekomendacijos, perduok informaciją rekomendacijos etapui.

## 1. Įvestis ir validavimas
- Pirmiausia naudok `prekiu-paieska` JSON iš ankstesnio žingsnio arba vartotojo pateiktus produktų duomenis. Jei jų nėra, paprašyk paieškos rezultato arba, jei vartotojas prašo ir paieškos, vykdyk `prekiu-paieska` pirmiausia.
- Priimk laukus: `category`, `budget_eur`, `country`, `requirements`, `preferences`, `search_date`, `checked_sellers`, `search_limitations`, `products[]`. Produkto laukai: `id`, `brand`, `model`, `price_eur`, `seller`, `product_url`, `manufacturer_url`, `features`, `availability`, `verified_price`, `price_conditions`, `alternative_offers`, `unverified_fields`.
- Patikrink JSON struktūrą, modelių unikalumą, kainų tipą ir `verified_price`. `null` reiškia nežinoma, ne 0. Neišgalvok trūkstamų laukų.
- Jei yra daugiau kaip 5 modeliai, paprašyk pasirinkti arba naudok pirmuosius 5, aiškiai pasakydamas, kad palyginimas ribotas; nekeisk vartotojo kriterijų.
- Jei produktas neatitinka aiškaus privalomo kriterijaus ar biudžeto, pažymėk kaip neatitinkantį ir nevertink jo kaip tinkamo. Neaiškaus atitikimo nelaikyk patvirtintu.
- Jei yra tik 1 modelis, pateik jo savybių santrauką ir paaiškink, kad tarpusavio palyginimui reikia bent 2.

## 2. Duomenų patikimumas
- Palyginimui naudok perduotus **patvirtintus** duomenis, išsaugok jų šaltinius ir paieškos datą. `verified_price: false` kainos nelaikyk patvirtinta.
- Kai savybė pateikta `features` tekste, naudok ją tik jei aišku, kuriam modeliui ji priklauso; neinterpretuok neaiškių skaičių ar rinkodaros teiginių kaip išmatuotų faktų.
- Jei duomenys seni, prieštaringi ar esminiai laukai trūksta, pasakyk apie tai. Kai vartotojas prašo dabartinių kainų arba jos būtinos teisingam palyginimui, naudok prieinamą interneto paiešką ir tikrink gamintojo bei tiesioginius pardavėjų puslapius; jei negali — žymėk „nepatikrinta“, o ne apsimesk atnaujinęs.
- Nepriskirk visiems modeliams savybės, kuri žinoma tik vienam. Neskaičiuok kainos ir pristatymo bendros sumos, jei pristatymas nežinomas.
- Skirk `nežinoma` nuo `nėra`. Nelygink skirtingais matavimo standartais gautų skaičių kaip tiesiogiai lygiaverčių.

## 3. Parink palyginimo kriterijus
- Visada įtrauk: **patvirtintą kainą EUR**, **privalomų reikalavimų atitikimą**, **prieinamumą** ir **duomenų patikimumą**.
- Pasirink 3–5 papildomus **kategorijai aktualius ir šaltiniais pagrįstus** kriterijus. Pavyzdžiai:
  - Ausinės: tipas / prigludimas, ANC, baterija (aiškiai nurodyk ANC būseną ir ar trukmė su dėklu), atsparumas, ryšys.
  - Telefonai: ekranas, baterija, kameros, atmintis, programinės įrangos palaikymas.
  - Kavos aparatai: tipas, gėrimai, priežiūra, talpa, matmenys.
- Jei vartotojas nurodė prioritetus, iškelk juos pirmiau. Nepaversk kategorijos pavyzdžių privalomais.
- Kiekybinius skirtumus skaičiuok tik iš palyginamų, patvirtintų skaičių; skaičiavimus suapvalink pagrįstai.

## 4. Palygink be galutinio sprendimo
1. Sudaryk lentelę: kriterijai eilutėse, modeliai stulpeliuose. Privalomų kriterijų būsenos: `atitinka`, `neatitinka`, `nepatvirtinta`.
2. Kiekvienam modeliui išskirk 1–3 **duomenimis pagrįstus** privalumus ir trūkumus / kompromisus. Trūkumas gali būti santykinis (pvz., didesnė kaina), tačiau nekurk nepatvirtintų minusų.
3. Trumpai įvardyk esminius kompromisus: kaina prieš savybes, komfortas prieš izoliaciją, funkcijos prieš baterijos laiką ir pan., tik jei tai leidžia duomenys.
4. Nepateik subjektyvių skaitinių balų ar galutinio reitingo. Gali nurodyti objektyvius kraštutinumus („mažiausia patvirtinta kaina tarp palygintų“), jei duomenys pilni ir palyginami.
5. Atskirai išvardyk nepatikrintas savybes, kainų sąlygas ir šaltinių prieštaravimus.

## 5. Išvestis kitam Skill
Pateik trumpą įvesties santrauką, palyginimo lentelę, kiekvieno modelio pliusus ir kompromisus, patikimumo ribas ir naudotus šaltinius.

Po jų pateik **validų JSON kodų bloke** `prekiu-rekomendacija` etapui. Išlaikyk visus gautus produkto identifikatorius, nuorodas ir pirminius duomenis, jų neištrink; papildyk palyginimo informacija. Schema:

```json
{
  "category": "belaidės ausinės",
  "budget_eur": 300,
  "country": "Lietuva",
  "requirements": [],
  "preferences": [],
  "search_date": "YYYY-MM-DD",
  "comparison_date": "YYYY-MM-DD",
  "criteria": ["kaina", "ANC", "baterija", "prieinamumas"],
  "search_limitations": [],
  "comparison_limitations": [],
  "products": [
    {
      "id": 1,
      "brand": "Apple",
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
      "unverified_fields": [],
      "criteria_values": {
        "kaina": null,
        "ANC": null,
        "baterija": null,
        "prieinamumas": "nežinoma"
      },
      "requirement_status": "nepatvirtinta",
      "advantages": [],
      "tradeoffs": [],
      "comparison_notes": []
    }
  ]
}
```

Tai **struktūros pavyzdys**, ne reali prekė. Tikrame rezultate:
- `criteria_values` raktai turi atitikti realius `criteria`; jei nėra duomenų — `null`.
- `requirement_status`: `atitinka`, `neatitinka`, `nepatvirtinta`.
- Perkelk visus gautus `products` šaltinių ir pasiūlymų laukus, taip pat `checked_sellers` (jei buvo); papildomų faktų neprasimanyk.
- Jei įvesties laukas nežinomas, naudok `null` ar `[]`, o ne išgalvotą reikšmę.
- JSON ir lentelė turi sutapti. Nenurodyk galutinio rekomenduojamo modelio.

## 6. Patikra prieš atsakymą
- Ar palyginami tie patys, nepasikartojantys modeliai, ne daugiau kaip 5?
- Ar įvertinti vartotojo privalomi kriterijai ir biudžetas?
- Ar nepatvirtinta kaina ar savybė nebuvo paversta faktu?
- Ar baterijos, ANC ir kiti skaičiai palyginami vienodomis sąlygomis?
- Ar pliusai ir kompromisai turi pagrindimą?
- Ar galioja JSON sintaksė ir išlaikytos pradinės nuorodos?
- Ar nebuvo išrinktas galutinis laimėtojas?

Testų scenarijai: [evals/evals.json](evals/evals.json).
