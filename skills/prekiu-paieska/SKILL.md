---
name: prekiu-paieska
description: Ieško ir atrenka iki 5 skirtingų prekių pagal vartotojo biudžetą bei kriterijus, patikrina kelių oficialių pardavėjų kainas ir gamintojo specifikacijas. Naudok, kai vartotojas prašo surasti ar atrinkti pirkti prekes Lietuvoje ar kitoje šalyje, prieš jas palyginant arba rekomenduojant.
---

# Prekių paieška (v2)

## Paskirtis
Atlik tik pirmą apsipirkimo darbo eigos etapą: surask, patikrink ir atrink iki 5 skirtingų modelių. Neskirk balų, nerikiuok pagal bendrą kokybę ir neteik galutinės pirkimo rekomendacijos — tai kitų Skills užduotis.

## 1. Nustatyk poreikius
- Išskirk kategoriją, maksimalų biudžetą EUR, pirkimo šalį, privalomus ir pageidaujamus kriterijus.
- Jei trūksta kategorijos arba biudžeto, paklausk tik būtino dalyko. Jei šalis nenurodyta, naudok Lietuvą.
- Nelaikyk pageidaujamos savybės privaloma, jei vartotojas to nenurodė. Nepridėk naujų apribojimų savo nuožiūra.
- Jei nurodyta kita valiuta, naudok patikimą konversijos kursą ir pažymėk jo datą arba paprašyk biudžeto EUR.

## 2. Ieškok plačiai, ne vienoje parduotuvėje
1. Naudok prieinamus interneto paieškos įrankius; be interneto nepristatyk rezultatų kaip šiuo metu patikrintų.
2. Kai įmanoma, patikrink **bent 3 skirtingų pardavėjų** oficialias svetaines pasirinktoje šalyje. Lietuvoje galimi paieškos pavyzdžiai: Senukai, Varle, Pigu, Topo Centras, Avitela. Tai pavyzdžiai, ne privalomas ar baigtinis sąrašas.
3. Prieš atrinkdamas penketuką, siek surinkti **8–10 skirtingų modelių kandidatų**, jei tokių rinkoje yra. Nesustok ties pirmais 5 rezultatais.
4. Jei modelis parduodamas keliur, kai įmanoma, patikrink **bent 2 pardavėjų** to paties tikslaus modelio pasiūlymus. Fiksuok abu pasiūlymus ir pasirink mažiausią **patvirtintą, vartotojui taikomą** kainą.
5. Neskirk privalomo kvotų skaičiaus galutiniams pardavėjams: visos 5 geriausios patvirtintos kainos gali būti iš vienos parduotuvės, jei taip iš tikrųjų yra. Tokiu atveju paaiškink, kuriose kitose parduotuvėse tikrinai.
6. Jei negali pasiekti 3 pardavėjų, 8 kandidatų ar 2 kainų kiekvienam modeliui, nesugalvok duomenų — įvardyk ribojimą ir tęsk su patikimais radiniais.

## 3. Patikrink šaltinius
- **Specifikacijos:** pirmenybę teik konkretaus modelio gamintojo svetainei arba oficialiai dokumentacijai.
- **Kaina, pardavėjas, pristatymo šalis, prieinamumas:** tikrink tiesioginį konkrečios prekės puslapį oficialioje pardavėjo svetainėje.
- Atskirai nurodyk įprastą kainą ir lojalumo, kupono, finansavimo ar kitų sąlygų kainą. Pagrindinei kainai naudok visiems prieinamą kainą, nebent vartotojas aiškiai leidžia sąlyginę.
- Kai kainą gali patvirtinti tik paieškos ištrauka, agregatorius ar neaiškus tarpininkas, pažymėk ją **nepatvirtinta** ir nenaudok jos kaip įrodyto biudžeto atitikimo.
- Skirk prieinamumo būsenas: `sandėlyje`, `užsakoma`, `išparduota`, `nežinoma`. Neskelbk `sandėlyje` be aiškaus patvirtinimo.
- Jeigu parduotuvė yra prekyvietė, nurodyk faktinį trečiosios šalies pardavėją, jei jis matomas; nevadink jo oficialiu gamintojo atstovu be įrodymų.
- Niekada neišgalvok URL, kainos, savybių, pardavėjo, atsargų ar patikros laiko.

## 4. Atrink modelius
- Atrink iki 5 **skirtingų tikslių modelių**, atitinkančių kategoriją, privalomus kriterijus ir biudžetą pagal patvirtintą kainą.
- Neįtrauk dublių iš skirtingų parduotuvių. Variantų (spalvų, komplektacijų) nelaikyk atskirais modeliais, nebent vartotojui tai svarbu.
- Jei prekė išparduota, jos nelaikyk įprastai prieinamu pirkimo pasiūlymu; jei tinkamų alternatyvų nėra, pateik tai kaip ribojimą.
- Rinkis pagal poreikių atitikimą, o ne pagal vienos parduotuvės pasiūlymų kiekį. Dar neatlik galutinio reitingavimo.
- Jei patvirtintų variantų mažiau nei 5, grąžink mažiau ir paaiškink kodėl; nekeisk biudžeto be vartotojo leidimo.
- Pristatymo mokesčius nurodyk atskirai, jei žinomi; jei nežinomi, aiškiai parašyk, kad į kainą neįtraukti.

## 5. Rezultato pateikimas
Pateik paieškos parametrus, datą ir lentelę:

| Nr. | Modelis | Mažiausia patvirtinta kaina EUR | Pardavėjas | Iki 3 savybių | Prieinamumas | Prekės nuoroda |

Po lentele trumpai pateik:
- kokias parduotuves tikrinai ir kiek jų buvo;
- kuriems modeliams patikrinai bent 2 pardavėjų kainas;
- oficialius gamintojų specifikacijų šaltinius;
- nepatikrintus laukus, nesutarimus, sąlygines kainas ir paieškos ribas;
- jei visi atrinkti pasiūlymai iš vienos parduotuvės — kodėl, nepainiodamas šio fakto su parduotuvių paieškos apimtimi.

Pateik validų JSON kodų bloke kitam Skill `prekiu-palyginimas`. Naudok tokią struktūrą (toliau — tik schemos pavyzdys, ne faktiniai produktai):

```json
{
  "category": "prekės kategorija",
  "budget_eur": 200,
  "country": "Lietuva",
  "requirements": [],
  "preferences": [],
  "search_date": "YYYY-MM-DD",
  "checked_sellers": [],
  "search_limitations": [],
  "products": [
    {
      "id": 1,
      "brand": "Gamintojas",
      "model": "Tikslus modelis",
      "price_eur": 0,
      "seller": "Pardavėjas",
      "product_url": "https://example.com/konkreti-preke",
      "manufacturer_url": null,
      "features": [],
      "availability": "nežinoma",
      "verified_price": false,
      "price_conditions": null,
      "alternative_offers": [
        {
          "seller": "Kitas pardavėjas",
          "price_eur": null,
          "product_url": null,
          "verified_price": false
        }
      ],
      "unverified_fields": []
    }
  ]
}
```

Tikrame JSON:
- naudok tik realiai rastus modelius ir tikras nuorodas;
- nežinomoms reikšmėms naudok `null`, o nerastiems pasiūlymams — tuščią `alternative_offers` masyvą;
- `verified_price` turi būti `true` tik patikrinus konkretų pardavėjo puslapį;
- neįtrauk į `products` modelio, kurio biudžeto atitikimas nepatvirtintas;
- JSON duomenys turi sutapti su lentele.

## 6. Kokybės patikra
Prieš atsakydamas patikrink:
1. Ar ieškota bent 3 pardavėjų svetainėse, jei tai įmanoma? Ar užfiksuoti ribojimai?
2. Ar buvo ieškoma platesnio kandidatų sąrašo, o ne sustota ties pirmais 5?
3. Ar kainos patikrintos konkrečių prekių puslapiuose ir, jei įmanoma, bent 2 pardavėjų?
4. Ar specifikacijos patvirtintos gamintojo šaltiniu arba aiškiai pažymėtos kaip nepatvirtintos?
5. Ar nėra pasikartojančių modelių, biudžeto viršijimo ir išgalvotų likučių?
6. Ar visos nuorodos veda į konkretų modelį, o JSON yra sintaksiškai taisyklingas ir atitinka lentelę?
7. Ar nebuvo pateikta galutinė rekomendacija?

Testų scenarijai: [evals/evals.json](evals/evals.json).
