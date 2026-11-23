# Extract EU allergens

Prompt template. Given a German ingredient list (`Zutaten`), return the EU allergens present as ingredients or named in a “kann Spuren von…” (may contain traces) clause.

## Input

```text
{{ingredient_list}}
```

Paste the German ingredient declaration in place of `{{ingredient_list}}`. The list may include parentheses, emphasis (`**Weizen**`, ALL CAPS), E-numbers, and a trailing “kann Spuren enthalten” clause.

## Prompt

```text
You extract allergens from a German ingredient list under EU Regulation (EU) No 1169/2011, Annex II (the 14 substances that must be declared).

Ingredient list:
"""
{{ingredient_list}}
"""

Return a JSON array and nothing else. No markdown, no explanation.

Each item must be exactly one of these canonical ids, in this order, with duplicates removed:

1. cereals_containing_gluten
2. crustaceans
3. eggs
4. fish
5. peanuts
6. soybeans
7. milk
8. nuts
9. celery
10. mustard
11. sesame
12. sulphur_dioxide
13. lupin
14. molluscs

If none of the 14 are present as ingredients or as possible traces, return exactly:
["NONE"]

Rules:
- Match German names, common synonyms, and parenthetical declarations (for example "Weizenmehl (Weizen)", "Magermilchpulver", "Sojalecithin").
- Treat bold, asterisks, and capitalisation as emphasis only. They do not change the allergen.
- Count an allergen when it is an ingredient of the product or when it is named in a precautionary traces clause such as "kann Spuren von … enthalten", "kann … enthalten", or "hergestellt in einem Betrieb, der … verarbeitet".
- Include trace allergens in the same array as ingredient allergens. Keep the canonical order above and remove duplicates, including when the same allergen appears both as an ingredient and as a trace.
- If the text names no Annex II allergen as an ingredient and no Annex II allergen in a traces clause, return ["NONE"].
- Do not invent allergens that are not supported by the text.
- These are not Annex II allergens on their own: Kokosnuss, Pinienkerne, Kastanie, Muskat, Mais, Reis, Buchweizen, Glucose, Dextrose, Maltodextrin (unless the source cereal is a gluten cereal, e.g. "Weizen"), Sonnenblumenlecithin, Rapsöl, Kakao.

Map ingredients to ids using these German cues (not exhaustive):

- cereals_containing_gluten: Gluten, Weizen, Roggen, Gerste, Hafer, Dinkel, Kamut, Khorasan, Grünkern, Einkorn, Emmer, Triticale, Weizenmehl, Roggenmehl, Gerstenmalz, Haferflocken, Dinkelmehl, Weizengluten, Weizenstärke when the cereal is named
- crustaceans: Krebstiere, Krabbe, Garnele, Shrimp, Hummer, Languste, Flusskrebs, Scampi, Krill
- eggs: Ei, Eier, Eigelb, Eiklar, Vollei, Hühnerei, Eipulver
- fish: Fisch, Kabeljau, Lachs, Thunfisch, Forelle, Hering, Sardine, Anchovis, Sardelle, Surimi, Fischgelatine
- peanuts: Erdnuss, Erdnüsse, Erdnussöl, Erdnussbutter, Erdnussmus
- soybeans: Soja, Sojabohne, Sojalecithin, Sojaprotein, Sojasauce, Tofu, Edamame, Tempeh
- milk: Milch, Laktose, Lactose, Butter, Sahne, Rahm, Käse, Molke, Joghurt, Quark, Schmand, Kasein, Casein, Milcheiweiß, Magermilch, Vollmilch, Buttermilch, Milchpulver
- nuts: Schalenfrüchte, Mandel, Haselnuss, Walnuss, Cashew, Pekannuss, Paranuss, Pistazie, Macadamia, Queenslandnuss
- celery: Sellerie, Knollensellerie, Staudensellerie, Selleriesalz, Selleriesamen
- mustard: Senf, Senfkörner, Senfmehl, Senfsauce
- sesame: Sesam, Sesamsamen, Sesamöl, Tahin, Tahini
- sulphur_dioxide: Schwefeldioxid, Schweflige Säure, Sulfit, Sulfite, E220, E221, E222, E223, E224, E226, E227, E228
- lupin: Lupine, Lupinen, Lupinenmehl, Lupinenprotein
- molluscs: Weichtiere, Muschel, Miesmuschel, Auster, Tintenfisch, Kalmar, Oktopus, Krake, Schnecke, Jakobsmuschel, Venusmuschel

Output examples:

Zutaten: Wasser, Weizenmehl, Magermilchpulver, Salz.
["cereals_containing_gluten","milk"]

Zutaten: Tomaten, Zwiebeln, Olivenöl, Salz. Kann Spuren von Senf enthalten.
["mustard"]

Zutaten: Zucker, Mandeln (28%), Hühnereigelb, Sojalecithin.
["eggs","soybeans","nuts"]

Zutaten: Weizenmehl, Zucker, Butter (Milch), Hühnereier. Kann Spuren von Erdnüssen enthalten.
["cereals_containing_gluten","eggs","peanuts","milk"]

Zutaten: Tomaten, Zwiebeln, Olivenöl, Salz.
["NONE"]
```
