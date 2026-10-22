---
name: prekiu-rekomendacija
description: Pagal prekiu-palyginimas v2 JSON, vartotojo prioritetus ir įrodymų patikimumą parenka pagrįstą pirkimo rekomendaciją arba aiškiai susilaiko, jei duomenų nepakanka. Naudok, kai vartotojas prašo iš palygintų prekių išrinkti tinkamiausią, paaiškinti pasirinkimą ir alternatyvas.
---

# Prekių rekomendacija (v1)

## Paskirtis
Trečias apsipirkimo etapas: `prekiu-paieska` → `prekiu-palyginimas` v2 → **`prekiu-rekomendacija`**. Tikslas – pagal vartotojo reikalavimus ir patikimus įrodymus pasiūlyti tinkamiausią variantą arba sąžiningai nurodyti, kad galutinei rekomendacijai duomenų nepakanka. Nesupainiok pigiausio, populiariausio ir tinkamiausio.

## 1. Įvestis ir suderinamumas
- Pirmiausia naudok `prekiu-palyginimas` rezultatą su `schema_version: "2.0"`. Privalomi laukai: `products[]`, `requirements[]`, `preferences[]`, `budget_eur`, `comparison.criteria[]`, `comparison.by_product_id`, `comparison.issues[]`. Kiti laukai išsaugomi, jei perduoti.
- Susiek `comparison.by_product_id[String(product.id)]` su kiekvienu `products[]` elementu. Patikrink, ar nėra trūkstamų, papildomų ar dubliuotų ID. Nepasikliauk masyvų eilės sutapimu.
- Jei gautas `prekiu-paieska` JSON arba senas `prekiu-palyginimas` formatas, **nespėk**, kad tai v2. Paprašyk atlikti palyginimo v2 etapą arba, jei vartotojas aiškiai prašo atlikti visą eigą, vykdyk jį pirmiau.
- Patikrink modelio tapatybę (tikslus kodas), biudžetą, kainos tipą, prieinamumą, `requirement_status`, `evidence_quality`, `normalized_metrics`, `issues` ir šaltinių nuorodas.
- Jei trūksta esminio vartotojo prioriteto ir dėl jo keistųsi pasirinkimas, užduok vieną tikslų klausimą; kitu atveju pateik aiškiai įvardytas sąlygines alternatyvas.

## 2. Privalomas tinkamumo filtras
- Atmesk iš pagrindinės rekomendacijos modelius su `requirement_status: "neatitinka"`, įrodyta kaina virš biudžeto, išparduotu pasiūlymu (jei reikia pirkti dabar) arba įrodyta neatitiktimi privalomai savybei.
- `requirement_status: "nepatvirtinta"` **nėra** `atitinka`. Modeliai su nepatvirtintu privalomu kriterijumi negali būti pateikti kaip besąlygiškai tinkami.
- `verified_price: true` yra ankstesnės paieškos deklaracija, o ne šio etapo patikra. Nežinoma / nepatvirtinta kaina negali pagrįsti tvirtos biudžeto atitikties.
- Jei produkto tapatybė arba kritinė specifikacija nepatvirtinta ar prieštaringa, sustabdyk **besąlygišką** jo rekomendaciją ir įrašyk `needs_verification`.
- Jei visi modeliai atmesti ar nepatvirtinti, grąžink `decision: "insufficient_evidence"`, o ne išgalvotą laimėtoją. Nepraplėsk biudžeto be leidimo.

## 3. Įrodymų patikimumas ir aktualumas
- `confirmed`: šiame arba aiškiai dokumentuotame etape atvertas tikslus patikimas šaltinis; `reported`: perduota iš ankstesnio etapo; `unverified`: nėra pakankamo pagrindo; `conflicting`: šaltiniai prieštarauja. URL egzistavimas savaime nėra patikra.
- Jei reikalinga galutinė **šiandien perkamos prekės** rekomendacija, pagal galimybes dar kartą patikrink pasirinkto modelio tikslią kainą, prieinamumą ir komplektaciją tiesioginiame pardavėjo puslapyje; technines kritines savybes – oficialiame gamintojo šaltinyje. Pažymėk patikros datą.
- Jei prieiga prie interneto negalima, neskelbk šviežių kainų ar likučių; rekomendaciją formuluok kaip **sąlyginę pagal ankstesnius duomenis**.
- Nepatikrintų pigesnių `alternative_offers` nenaudok kaip pagrindinės kainos. Lojalumo, finansavimo ir kuponų kainas atskirk. Nežinomų pristatymo mokesčių neįskaičiuok.
- `comparison.issues` nagrinėk kiekvienam modeliui: įvardyk, ar problema kritinė sprendimui; kritinių konfliktų nenutylėk.

## 4. Skaidri pasirinkimo logika
1. Iš pradžių taikyk **griežtus vartotojo reikalavimus**, paskui jo pageidavimus. Nekurk naujų privalomų kriterijų.
2. Palygink likusius pagal vartotojo išreikštus prioritetus; kai prioritetų nėra, aiškiai įvardyk praktinius kompromisus (kaina, svarbiausios kategorijos funkcijos, prieinamumas, įrodymų patikimumas). Neskirk savavališkų procentinių svorių ar skaitinių balų.
3. Naudok tik palyginamus `normalized_metrics`; jei `comparable: false`, nepriimk sprendimo remdamasis to rodiklio skaitiniu pranašumu.
4. Nurodyk **vieną pagrindinį pasirinkimą**, jei įrodymų pakanka ir prioritetai leidžia, bei iki 2 prasmingų alternatyvų su aiškiais pasirinkimo scenarijais. Jei keli variantai lygiaverčiai, pasakyk, kokia sąlyga juos atskirtų; neapsimesk turįs vienareikšmį laimėtoją.
5. Paaiškink pasirinkimo priežastis remdamasis konkrečiomis savybėmis, kainų skirtumais ir šaltiniais. Venk „geriausias visiems“, „garantuotai pigiausias“ be įrodymų.

## 5. Rezultato pateikimas
Pateik:
- **Sprendimo būsena**: `recommended`, `conditional` arba `insufficient_evidence`.
- **Pagrindinis modelis** (jei įmanoma): tiksli identifikacija, patvirtintos / ankstesnės paieškos kainos statusas, pardavėjas ir tikra produkto nuoroda.
- **Kodėl**: 2–4 argumentai pagal reikalavimus ir vartotojo prioritetus; 1–2 svarbūs kompromisai.
- **Alternatyvos**: iki 2 modelių ir kada verta rinktis juos.
- **Ribos ir patikros prieš pirkimą**: kritiniai nepatvirtinti faktai, prieinamumas, sąlyginės kainos, pristatymas, data.
- Jei `insufficient_evidence`, aiškiai nurodyk, **ką konkrečiai būtina patikrinti**, ir nepateik apsimestinės rekomendacijos.

## 6. Struktūruotas rezultatas
Po žmogui suprantamos santraukos pateik validų JSON kodų bloke. Pavyzdys (ne faktiniai duomenys):

```json
{
  "schema_version": "1.0",
  "source_comparison_version": "2.0",
  "decision": "conditional",
  "recommended_product_id": 1,
  "reasons": ["Atitinka patvirtintus privalomus kriterijus"],
  "tradeoffs": ["Prieš pirkimą patikrinti likutį"],
  "alternatives": [
    {"product_id": 2, "when_to_choose": "Jei svarbesnė kita funkcija"}
  ],
  "needs_verification": [
    {"product_id": 1, "field": "availability", "reason": "Likutis nepatvirtintas"}
  ],
  "price_checked_at": null,
  "sources": [],
  "limitations": []
}
```

Taisyklės:
- `recommended_product_id` turi būti vienas iš įvesties `products[].id` arba `null`; esant `insufficient_evidence` – `null`.
- `alternatives[].product_id` turi priklausyti įvesties modeliams ir nesutapti su pagrindiniu.
- `decision: "recommended"` tik kai tapatybė, visi privalomi kriterijai ir sprendimui kritiniai faktai patvirtinti; jei kainos ar likučiai neatnaujinti, bet galima pateikti sąlyginį variantą – `conditional`.
- Jei įrodymų nepakanka net sąlyginei išvadai – `insufficient_evidence`.
- `price_checked_at` yra tikros kainos patikros data/laikas arba `null`, niekada automatiškai neperkelk `search_date` kaip naujos patikros.
- `sources` pateik tik realias, faktiškai naudotas nuorodas; nežinomus laukus žymėk `null`.
- Nepateik pirkimo / užsakymo kaip atlikto veiksmo. Be vartotojo leidimo nieko neužsakyk.

## 7. Kokybės patikra
- Ar `schema_version: "2.0"` įvestis teisingai susieta pagal ID?
- Ar atskirti `neatitinka`, `nepatvirtinta` ir `atitinka`?
- Ar atmestos nepatvirtintos nuolaidos ir nepalyginami skaičiai?
- Ar kiekvienas sprendimo argumentas remiasi turimu faktu ir įrodymų statusu?
- Ar kritinės `issues` problemos sustabdė nepagrįstą tvirtą rekomendaciją?
- Ar JSON yra validus, ID egzistuoja ir sprendimo būsena atitinka išvadą?
- Ar nebuvo apsimestinės aktualių kainų patikros ar pirkimo?

Testavimo scenarijai: [evals/evals.json](evals/evals.json).
