---
name: prekiu-workflow
description: Paprasta prekių paieška vienu prašymu: automatiškai suranda, palygina ir rekomenduoja prekes pagal biudžetą. Vartotojui pateikia trumpą suprantamą atsakymą be JSON ir techninių detalių.
---

# Prekių workflow (v2) – tik paprastas variantas

## Naudojimas
Vartotojui pakanka parašyti: „Surask belaides ausines iki 200 €“ arba „Rekomenduok kavos aparatą iki 300 €“. Nereikalauk iš jo žinoti Skills pavadinimų, pateikti JSON ar pačiam atlikti atskirų etapų.

## Viena privaloma eiga
Kiekvieno naujo vykdymo pradžioje iš GitHub `evelinapal/testing-skills` perskaityk naujausius tris failus ir vykdyk jų taisykles **nuosekliai**, ne lygiagrečiai:
1. `skills/prekiu-paieska/SKILL.md` – surask iki 5 skirtingų modelių ir sukurk `search_result`.
2. `skills/prekiu-palyginimas/SKILL.md` – iš pilno paieškos rezultato sukurk `comparison_result` su `schema_version:"2.0"`.
3. `skills/prekiu-rekomendacija/SKILL.md` – iš pilno palyginimo sukurk `recommendation_result` su `source_comparison_version:"2.0"`.

Šis failas tik koordinuoja tris Skills ir vartotojui rodomą atsakymą. Jų kokybės taisyklių neperrašyk ir neapeik. Jei kurio nors failo negali perskaityti, nurodyk problemą; neapsimesk, kad vykdei.

## Ką suprasti iš užklausos
- Nustatyk prekių kategoriją, biudžetą, šalį (jei nenurodyta – Lietuva), aiškiai pasakytus privalomus kriterijus ir pageidavimus.
- Jei trūksta kategorijos arba biudžeto, paklausk vieno trūkstamo esminio dalyko. Jei papildomų prioritetų nėra, **neklausk** – neišgalvok prioritetų, savavališkų balų ar svorių.
- Nepaversk ANC, prekės ženklo, spalvos ar kitų neprašytų savybių privalomais kriterijais.

## Patikimumo taisyklės
- Paieškoje siek 8–10 kandidatų, patikrink bent 3 pardavėjų svetaines ir, jei įmanoma, bent 2 tiesioginius pasiūlymus kiekvienam atrinktam modeliui. Jei nepavyko, trumpai nurodyk ribą, bet **neišgalvok** rezultatų.
- Kainą laikyk patvirtinta tik iš tiksliai identifikuoto modelio **tiesioginio pardavėjo puslapio**; gamintojo savybes tikrink konkretaus modelio oficialioje specifikacijoje. Paieškos ištrauka, agregatorius ar vien URL nėra patvirtinimas.
- Į patvirtintai biudžetą atitinkančių `products[]` sąrašą neįtrauk modelių be patvirtintos kainos. Nežinomą likutį pažymėk kaip nežinomą; lojalumo ar kupono kainos nelaikyk įprasta.
- Išsaugok produkto ID, visas nuorodas ir patikros būsenas tarp etapų. `comparison.by_product_id[String(id)]` turi sutapti su `products[].id`; `criteria_values` raktai – su `comparison.criteria`.
- Nepalyginamų matavimų (pvz., baterijos su dėklu ir be dėklo) nevertink kaip tiesioginio pranašumo. Neišspręstus `issues` perduok rekomendavimo etapui.
- Jei rekomenduojant aptinkamas naujas faktas, prieštaraujantis palyginimui, pataisyk paveiktą paieškos / palyginimo dalį prieš pateikdamas išvadą.
- Rekomendacijos sprendimas: `recommended` tik patvirtinus kritinius faktus; `conditional` jei yra naudingas, bet ne iki galo patikrintas pasirinkimas; `insufficient_evidence` jei duomenų nepakanka net preliminariam pasirinkimui. Nežadėk pigiausios rinkoje kainos be įrodymų.

## Vienintelis vartotojui rodomas atsakymo formatas
Pateik tik šią **trumpą ir suprantamą** struktūrą:

**Rekomenduoju [modelį] – [kaina] € ([pardavėjas]).**
Jei `conditional`, rašyk **„Preliminariai rekomenduoju“**. Jei `insufficient_evidence`, aiškiai rašyk **„Patikimos rekomendacijos dar nėra“** ir pasakyk, ką reikia patikrinti.

**Kodėl?** 2–3 konkrečios, šaltiniais pagrįstos priežastys.

**Palyginimas:** kompaktiška 3–5 modelių (arba mažiau, jei tiek patvirtinta) lentelė su kaina, 2–3 svarbiausiomis savybėmis ir pardavėjo nuoroda.

**Alternatyvos:** iki 2, su paprastu paaiškinimu „rinkis, jei...“.

**Prieš perkant:** 1–3 esminiai nepatvirtinti faktai, pvz., pristatymo kaina ar likutis. Šaltinius atskirk: oficialus gamintojas, tiesioginis pardavėjas, trečiosios šalies apžvalga. Nurodyk patikros datą ir paieškos ribas vienu trumpu sakiniu.

**Nerodyk JSON, schemų, vidinių etapų būsenų, programinio testavimo informacijos ar ilgų techninių aiškinimų.** Visų trijų etapų duomenis vis tiek nuosekliai paruošk ir validuok prieš atsakydamas; jei patikra nepavyko, neskelbk jos atlikta.

## Paskutinė kontrolė
Ar perskaitytos visos instrukcijos? Ar A → B → C įvykdyta iš eilės? Ar kainos, tapatybė ir šaltiniai tikrai patikrinti? Ar produkto ID, JSON schemos ir kritinės problemos išsaugotos? Ar galutinė išvada sąžininga ir atsakymas paprastas? Niekada neužsakyk prekės be aiškaus vartotojo leidimo.
