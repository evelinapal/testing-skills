---
name: prekiu-paieska
description: Suranda iki 5 tinkamų prekių pagal vartotojo biudžetą ir poreikius oficialiose gamintojų bei pardavėjų svetainėse. Naudok, kai prašoma surasti, atrinkti ar ieškoti pirkti prekes Lietuvoje arba kitoje nurodytoje šalyje, prieš jas palyginant ir rekomenduojant.
---

# Prekių paieška

## Paskirtis
Atlik tik pirmą apsipirkimo darbo eigos etapą: surask ir patikrink iki 5 skirtingų prekių modelių. Nevertink jų balais ir nerink vieno laimėtojo; tai vėlesnių skills užduotis.

## 1. Įvestis
- Nustatyk prekės kategoriją, didžiausią biudžetą EUR, privalomus kriterijus, pageidaujamas savybes ir pirkimo šalį.
- Jei kategorija arba biudžetas neaiškūs, paklausk tik trūkstamų esminių duomenų. Jei šalis nenurodyta, naudok Lietuvą.
- Jei biudžetas nurodytas kita valiuta, konvertuok tik turėdamas patikimą kursą arba paprašyk EUR ribos.

## 2. Šaltinių paieška
- Naudok prieinamus internetinės paieškos įrankius. Jei jų nėra, aiškiai pranešk, kad realios paieškos atlikti negali.
- Technines savybes tikrink oficialiame gamintojo modelio puslapyje arba dokumentacijoje.
- Kainą, pardavėją, šalį ir prieinamumą tikrink tiesioginiame oficialios parduotuvės prekės puslapyje. Oficialus pardavėjo puslapis nebūtinai yra gamintojo svetainė.
- Nesiremk kainų agregatorių, forumų, reklaminių straipsnių ar socialinių tinklų informacija kaip galutiniu patvirtinimu.
- Paieškos rezultato ištrauka nėra pakankamas kainos patvirtinimas: kai įmanoma, atverk konkretų prekės puslapį.
- Niekada neišgalvok nuorodų, kainų, modelių, likučių ar specifikacijų.

## 3. Atranka
- Ieškok 5 skirtingų modelių, kurie atitinka kategoriją, privalomus kriterijus ir neviršija biudžeto.
- Jei randi daugiau, pasirink 5 geriausiai atitinkančius aiškiai išsakytus vartotojo poreikius, bet neatlik galutinio palyginimo.
- Skirtingi to paties modelio pardavėjai nėra atskiros prekės.
- Jei patvirtintų variantų mažiau nei 5, grąžink tik rastus ir paaiškink, ko trūko. Nekeisk biudžeto ar privalomų kriterijų be leidimo.
- Neskaičiuok pristatymo kainos į prekės kainą, jei ji nežinoma; aiškiai pažymėk, kad pristatymas neįtrauktas.

## 4. Duomenų tikrinimas
Kiekvienam modeliui surink:
- gamintoją, tikslų modelį;
- kainą EUR, parduotuvės pavadinimą ir tiesioginę prekės nuorodą;
- iki 3 svarbiausių techninių savybių ir, jei yra, oficialią gamintojo nuorodą;
- prieinamumą (patvirtintas / nepatvirtintas / nežinomas);
- patikrinimo datą ir nepatikrintus laukus.

Jei šaltiniai prieštarauja, pažymėk neatitikimą. Jei kaina nepatvirtinta, nenaudok jos kaip patvirtinto biudžeto atitikimo.

## 5. Rezultatas
Pirmiausia pateik trumpą paieškos parametrų santrauką ir lentelę:
| Nr. | Gamintojas ir modelis | Kaina EUR | Parduotuvė | 3 savybės | Prekės nuoroda | Patikra |
Po lentele išvardyk oficialius šaltinius, nepatikrintus duomenis, paieškos ribas ir datą. Nurodyk, jei rasta mažiau nei 5 modeliai.

Tada pateik galiojantį JSON kodų bloke, tinkamą perduoti kitam skill:
```json
{
  "category": "belaidės ausinės",
  "budget_eur": 100,
  "country": "Lietuva",
  "requirements": [],
  "search_date": "YYYY-MM-DD",
  "products": [
    {
      "id": 1,
      "brand": "tikras gamintojas",
      "model": "tikras modelis",
      "price_eur": 79.99,
      "seller": "tikras pardavėjas",
      "product_url": "https://oficialus-pardavejas.example/preke",
      "manufacturer_url": null,
      "features": [],
      "availability": "nežinomas",
      "verified_price": true,
      "unverified_fields": []
    }
  ]
}
```
Šis JSON yra tik struktūros pavyzdys, ne tikri paieškos duomenys. Realiame atsakyme įrašyk tik patikrintus faktus, nežinomoms reikšmėms naudok null, o products masyve pateik tik rastus modelius.

## 6. Patikra prieš atsakymą
- Ar nėra daugiau kaip 5 modelių ir dublių?
- Ar kiekvieno modelio kaina patikrinta oficialiame pardavėjo puslapyje ir neviršija biudžeto?
- Ar kiekvienas teiginys apie specifikacijas pagrįstas oficialiu šaltiniu?
- Ar JSON sintaksiškai teisingas ir atitinka lentelę?
- Ar aiškiai atskirti patikrinti faktai ir nepatvirtinti duomenys?

Testavimo scenarijai aprašyti [evals/evals.json](evals/evals.json).
