# Playbooky — úkol → sekvence toolů

Konkrétní postupy pro časté úkoly. Otevři ten, který sedí na záměr uživatele.
Parametry a footguny jednotlivých toolů jsou v [`tools.md`](tools.md).

Společná pravidla pro všechny playbooky:
- **Šetři kredity** — drž se naznačeného počtu dotazů, u rozšíření se zeptej.
- **Atribuuj** čísla Marketing Mineru a **připiš datum** stažení.
- **Vracej závěr, ne JSON** — strukturovaná čísla + 1 takeaway, co s tím.

---

## 1) Keyword research před obsahem
**Záměr:** „Chci psát o *běžeckých botách* — co lidi hledají a jak to pokrýt?"

1. `keyword_search_volume` (`lang`, `keyword` = seed) → je o to vůbec zájem?
   `search_volume: null` → seed přeformuluj, nemá poptávku.
2. `keyword_suggestions` (`lang`, `keyword` = seed, `with_keyword_data: true`)
   → hlavní related slova + `difficulty` + `serp_features`. (V čerstvých nikách
   bývá `difficulty` z velké části `null` — nespoléhej na ni, viz `tools.md` →
   Limity dat.)
3. `keyword_suggestions` (… `suggestions_type: "questions"`) → otázky pro FAQ /
   People Also Ask sekce a H2/H3.
4. Volitelně `keyword_suggestions` (… `suggestions_type: "trending"`) → témata na
   vzestupu, kterým dát prostor dřív.

**Výstup:** návrh struktury článku (nadpis + H2/H3 + FAQ) s hledaností u každého
bloku, oddělené „má smysl hned" vs. „spíš později" podle **objem × trend × vlastní
reálné pozice** (např. z Google Search Console). `difficulty` přidej do úvahy jen
tam, kde ji MM vrátil — v mladých nikách často chybí.
**Takeaway:** 1 podtéma s reálnou poptávkou, které stojí za to napsat první.

---

## 2) Content gap proti konkurenci
**Záměr:** „Na jaká slova je vidět konkurent a já ne?"

1. (Volitelně) `website_serp_competition_domains` (cílový web) → když uživatel
   nezná konkurenty, vyber 1–3 s nejvyšším `same_percentage`.
2. `website_content_gap` (`lang`, `type`/`target` = tvůj web, `other_domain_1..3`
   = konkurenti) → slova, kde rankují oni a ty ne.

**Výstup:** **priorizovaný** seznam (objem × relevance), ne dump. U každého slova
`search_volume`, kolik konkurentů na něj cílí (`number_of_competitors`) a kdo na
jaké pozici. Vysoký objem + hodně konkurentů = ověřená díra v tvém obsahu.
**Takeaway:** top 3–5 slov, na která chybí stránka, seřazená podle dopadu.

---

## 3) Rozbor konkurenta / retrospektiva
**Záměr:** „Jak velký je *husky.cz* a jak roste oproti *alza.cz*?"

1. `website_stats` (`type: "subdomain"`, `target` = web) → snapshot teď, rozpad
   po `result_type` (iteruj přes všechny, ne jen organic).
2. `website_stats_range` (`target` = web, `period: "monthly"`, `competitors` =
   **JSON-string** `'[{"target":"alza.cz","type":"subdomain"}]'`) → vývoj v čase
   vedle sebe.
3. `website_top_pages` (`target` = web) → co konkrétně mu táhne návštěvnost.
4. Volitelně `website_suggestions` (`target` = web) → na jaká slova je vidět.

**Výstup:** kde web je, jestli roste/padá, čím (které stránky/slova). Trend čti
z `estimated_traffic_change`.
**Takeaway:** 1 věc, kde konkurent vyhrává a kde je prostor ho dohnat.

---

## 4) Mapa konkurence v SERP
**Záměr:** „Kdo jsou moji SEO konkurenti?"

1. `website_serp_competition_domains` (`target` = tvůj web) → konkurenti podle
   překryvu slov, seřazení od nejsilnějšího (`same_percentage`).
2. Pro page-level souboj `website_serp_competition_pages` → konkrétní URL, které
   přebít.

**Výstup:** seznam reálných SERP konkurentů (ne „kdo je v oboru", ale kdo soupeří
o stejná slova) + `same_keywords` / `same_percentage`.
**Takeaway:** 1–2 konkurenti, proti kterým má smysl postavit content gap (→ #2).

---

## 5) Kanibalizace
**Záměr:** „Nepereme se sami se sebou o stejné slovo?"

1. `website_cannibalization` (`type`/`target` = tvůj web).

**Výstup:** slova s `number_of_landing_pages > 1`, doporučená kanonická stránka
(`best_landing_page`) a soupeřící URL. Čím vyšší `number_of_landing_pages`
+ `movements`, tím horší.
**Takeaway:** konkrétní dvojice/trojice URL k sloučení nebo přesměrování, od
nejbolavější.

---

## 6) Pojmenování podle hledanosti
**Záměr:** „Mám kategorii/produkt/landing — pojmenovat A, nebo B?"

1. `keyword_search_volume` pro variantu A.
2. `keyword_search_volume` pro variantu B (+ případně 3. kandidát).

**Výstup:** porovnání hledaností (a CPC) jednotlivých variant.
**Takeaway:** vyber výraz s reálnou poptávkou — i když interně říkáte něčemu
jinak. Pozor: tohle je nejlevnější a nejčastější use-case, ideální „rychlý dotaz".

---

## 7) Nejsilnější stránky webu
**Záměr:** „Které naše stránky táhnou nejvíc / kam dát update?"

1. `website_top_pages` (`type`/`target` = tvůj web).
2. Volitelně `website_top_subdomains` (`type: "subdomain"` na apex) → rozpad po
   subdoménách (blog vs e-shop apod.).

**Výstup:** žebříček URL podle `estimated_traffic`, s `top_kw` a `avg_position`.
**Takeaway:** 1–2 stránky s velkým objemem a zhoršující se `avg_position` =
nejlepší kandidáti na refresh.

---

## Rychlý přehled (bare call — bez konkrétního úkolu)
Když uživatel jen hodí doménu nebo téma a nechce nic specifického:

1. 1 seed → `keyword_search_volume` + `keyword_suggestions`.
2. 1 konkurent → `website_serp_competition_domains` nebo rovnou `website_content_gap`.
3. **1 takeaway** (např. „tohle podtéma má poptávku a nikdo z konkurence ho
   nepokrývá") a nabídni, kam dál.

Drž se pár dotazů — tohle má být levný rychlý vhled, ne plný audit.
