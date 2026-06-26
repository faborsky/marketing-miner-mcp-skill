# Marketing Miner MCP — mapa toolů

Přesná reference k 11 toolům MCP serveru. **Vždy si reálné názvy a parametry ověř
přes `ToolSearch`** (prefix `mcp__marketing-miner__*`, případně jiný, pokud byl
konektor pojmenovaný jinak) — schémata se můžou měnit. Tahle stránka je mapa
a hlavně sbírka footgunů, na kterých se nejčastěji chybuje.

---

## Sdílené parametry (platí napříč tooly)

### `lang` (povinný všude)
Cílový trh: `cs`, `sk`, `pl`, `hu`, `ro`, `gb`, `us`. Default pro česky/slovensky
mluvícího uživatele = `cs` / `sk`. Je to **trh**, ne jazyk odpovědi.

### `type` + `target` (povinné u všech `website_*` toolů) — #1 zdroj chyb
Špatná volba `type` = data o úplně jiném rozsahu webu.

| `type` | Co znamená | `target` příklad | Pozn. |
|---|---|---|---|
| `subdomain` | **Celý web** vč. všech subdomén — **DEFAULT** pro „jak velký je X" | `alza.cz` | Použij, když uživatel jmenuje web obecně |
| `domain` | **Jen jedna konkrétní subdoména**, izolovaně | `blog.example.com` | Jen když chce výhradně tu subdoménu |
| `exact` | **Jedna konkrétní URL** | `alza.cz/seo` + `scheme: "https"` | DEFAULT pro jakoukoli jednu URL |
| `prefix` | **Celá složka** pod cestou | `alza.cz/seo/` + `scheme: "https"` | Jen když chce „všechno pod /seo" |

- `scheme` (`https` / `http`) je potřeba u `exact` a `prefix`.
- Když si nejsi jistý → `subdomain` na apex doménu je bezpečný default.

### `limit`
Seznamy jsou capnuté (default **100** řádků). `rows_count` ve výstupu říká, kolik
jich reálně je. Když je `rows_count` výrazně vyšší než vrácený seznam, řekni to —
nedělej závěr „to je všechno".

### Atribuce
Každý tool si v popisu vyžaduje uvést zdroj. Když prezentuješ čísla:
*„Na základě dat z Marketing Mineru…"*. Ne jako vlastní odhad.

---

## Limity dat ověřené v praxi

Tyhle věci nejsou v popisech toolů — vyplynuly z reálného provozu na velkém
vzorku. Počítej s nimi, ať z dat neuděláš špatný závěr.

- **AI Overviews se u `cs` nevrací.** Na velkém vzorku českých dotazů se
  `ai_overviews` neobjevilo v `serp_features` ani jednou, přestože AI Overviews
  v Česku reálně běží. Ber to tak, že **MM přes MCP přítomnost AIO pro češtinu
  nedodává** — viditelnost v AI Overviews / AEO přes tenhle kanál **neměř**, na to
  použij jiný zdroj. Totéž nejspíš platí pro `ai_overviews` jako `result_type` ve
  `website_stats*` u cs. (Pro ostatní trhy neověřeno.)
- **`difficulty` má v čerstvých/mladých nikách řídké pokrytí.** U nově vznikajících
  témat a oborů vrací MM `difficulty` jen u malé části dotazů, zbytek je `null`.
  **Nestav prioritizaci na „nízká difficulty = easy win"** — v mladé nice to tiše
  selže (většina slov difficulty vůbec nemá). Prioritizuj **objem × trend ×
  vlastní reálné pozice** (např. z Google Search Console) a difficulty ber jen
  jako bonus, když ji MM vrátí.
- **YoY z nízké báze dělá astronomická %.** `yoy_change` v řádu statisíců procent
  (např. +900 000 %) neznamená „trh explodoval", ale „termín loni prakticky
  neexistoval" — je to **nový/vznikající dotaz**. Vždy to čti proti absolutnímu
  `search_volume`: malý objem + obří % = mladý termín, ne velká příležitost.

---

## Keyword tooly

### `keyword_search_volume`
Reálná měsíční hledanost + CPC + meziroční změna + 12měsíční sezónnost jednoho slova.

- **Params:** `lang`, `keyword` (2–80 znaků). **Jedno slovo na volání** — víc slov
  = víc volání (pozor na kredity).
- **Vrací (list):** `search_volume` (měsíční hledání; `null` = žádná poptávka →
  slovo zahoď), `cpc` `{value,text,currency_code}`, `yoy_change` (% vs. stejný
  měsíc loni; `+230` = více než ztrojnásobení; **pozor na nízkou bázi** — extrémní
  % = nový termín, ne explodující trh, viz Limity dat výše), `peak_month` (ISO
  datum vrcholu), `monthly_sv` (`"01"…"12"` → int, sezónní křivka). Prázdný list
  = žádná data.
- **Použij na:** ověření slova **před** vložením do nadpisu / meta / textu; A vs B
  pojmenování; čtení sezóny.

### `keyword_suggestions`
Související slova ze **seed slova** — nápady na témata, H2/H3, FAQ, long-tail.

- **Params:** `lang`, `keyword` (seed), `suggestions_type` (volitelně:
  `questions` = tázací fráze pro FAQ/PAA · `new` = nově vznikající dotazy ·
  `trending` = rychle rostoucí), `with_keyword_data` (bool; `true` = přidá
  `difficulty` 0–100 a `serp_features`, ale **dražší**).
- **Vrací:** seznam `keyword` + `search_volume`, `cpc`, (volitelně `difficulty`,
  `serp_features` jako `featured_snippet`/`people_also_ask`/`ai_overviews`),
  `yoy_change`, `peak_month`, `monthly_sv`.
- **Footgun:** **není** to to samé co `website_suggestions` (viz níže).
- **Pozor na pokrytí:** `difficulty` bývá v čerstvých nikách z velké části `null`
  a `ai_overviews` se u `cs` ve `serp_features` nevrací — viz Limity dat výše.
  Nestav na nich tvrdé závěry.

---

## Website tooly (vyžadují `type` + `target`)

### `website_suggestions` — ⚠️ matoucí název
Vrací slova, na která **doména už rankuje** v Google (ne návrhy!).

- **Params:** `lang`, `type`, `target` (+ `scheme` u url typů).
- **Vrací:** `keyword`, `url` (rankující stránka), `position` + `position_change`,
  `total_position`, `difficulty`, `search_volume`, `estimated_traffic` +
  `estimated_traffic_change`, `result_type`, `serp_features`, `status`.
- **Použij na:** „na jaká slova je vidět web X", „která slova nám rostou/padají".

### `website_stats`
Snapshot SEO návštěvnosti rozpadený **podle typu výsledku**.

- **Params:** `lang`, `type`, `target` (+ `scheme`).
- **Vrací:** `stats` = seznam po `result_type` (`organic`, `paid`, `shopping_ads`,
  `ai_overviews`, `local_pack`, …), každý s `estimated_traffic`,
  `estimated_traffic_change` (m/m), `number_of_keywords`; +
  `estimated_traffic_sum`, `estimated_traffic_change_sum`, `number_of_keywords_sum`.
- **Footgun:** **nehardcoduj** jen organic/paid — iteruj přes všechny vrácené
  `result_type`. (U `cs` ale nečekej `ai_overviews` — viz Limity dat výše.)

### `website_stats_range`
Časová řada návštěvnosti za ~14 měsíců, volitelně s konkurenty na téže ose.

- **Params:** `lang`, `type`, `target`, `period` (`daily`/`weekly`/`monthly`),
  `competitors`.
- **Footgun (hlavní):** `competitors` musí být **JSON-string pole objektů**, ne
  čárkový seznam. Příklad: `'[{"target":"akcniceny.cz","type":"subdomain"}]'`.
- **Vrací:** `stats_range` = body `{date (lokalizovaný měsíc), result_type,
  estimated_traffic}` — **jeden záznam na každý `result_type` v měsíci**; pro jedno
  číslo za měsíc je sečti. `competitors` = stejný tvar per konkurent.
- **Použij na:** retrospektivy, „roste konkurent rychleji než my", sezónní graf.

### `website_top_pages`
Nejsilnější URL webu podle odhadované organické návštěvnosti.

- **Params:** `lang`, `type`, `target` (+ `scheme`).
- **Vrací:** `url`, `estimated_traffic` (+ change), `keyword_count`, `avg_position`
  (+ change), `search_volume` (součet), `top_kw` (slovo s největším přínosem).
- **Použij na:** kam směřovat update obsahu a interní prolinkování.

### `website_top_subdomains`
Rozpad návštěvnosti po subdoménách.

- **Params:** `lang`, `type`, `target`. **Nejlíp** `type: "subdomain"` na apex
  doménu (`example.com`), ať se to rozpadne přes všechny subdomény. `type: "domain"`
  na jednu subdoménu vrátí typicky jen jeden řádek.
- **Vrací:** `subdomain`, `estimated_traffic` (+ change), `keyword_count`,
  `avg_position` (+ change), `search_volume`, `top_kw`.

### `website_serp_competition_domains`
Domény, co soupeří s cílem v SERP (překryv klíčových slov), seřazené od nejsilnější.

- **Params:** `lang`, `type`, `target` (+ `scheme`).
- **Vrací:** `domain`, `subdomain`, `scheme`, `same_keywords` (počet slov, kde
  rankují oba), `same_percentage` (podíl slov cíle držených i konkurentem, 0–100).
- **Pozn.:** je to konkurence podle **překryvu v SERP**, ne podle oboru.

### `website_serp_competition_pages`
To samé, ale na **úrovni jednotlivých stránek** (URL), ne domén.

- **Params:** `lang`, `type`, `target` (+ `scheme`).
- **Vrací:** `url`, `same_keywords`, `same_percentage`.
- **Použij na:** „který konkrétní článek konkurence máme přebít".

### `website_content_gap`
Slova, na která rankují **konkurenti**, ale cílový web **ne** — klasická „o čem
psát dál".

- **Params:** `lang`, `type`, `target` (cílový web) + `other_domain_1` (povinný
  konkurent), `other_domain_2`, `other_domain_3` (volitelní; **max 3 konkurenti**).
- **Vrací:** `websites` = `{keyword, search_volume, number_of_competitors (kolik
  z konkurentů na slovo rankuje), items: [{domain, position, url}]}`.
- **Výstup priorizuj** (objem × relevance), nevracej syrový dump.

### `website_cannibalization`
Slova, o která soupeří **víc stránek téhož webu** (tříští si ranking).

- **Params:** `lang`, `type`, `target` (+ `scheme`).
- **Vrací:** `keyword`, `number_of_landing_pages` (`>1` = kanibalizace),
  `best_landing_page` (doporučená kanonická URL), `search_volume`, `movements`
  (jak často se rankující URL střídá; vyšší = nestabilnější), `items: [{url,
  min_position}]`.
- **Použij na:** co sloučit / přesměrovat. Vyšší `number_of_landing_pages`
  + `movements` = horší problém.

---

## Rychlý cheat-sheet „chci → tool"

| Chci zjistit | Tool |
|---|---|
| Hledanost + sezónu jednoho slova | `keyword_search_volume` |
| Nápady na podtémata / FAQ / trendy ze seedu | `keyword_suggestions` |
| Na co web už rankuje | `website_suggestions` |
| Jak velký web je teď | `website_stats` |
| Jak web roste v čase (+ konkurenti) | `website_stats_range` |
| Nejsilnější stránky webu | `website_top_pages` |
| Rozpad po subdoménách | `website_top_subdomains` |
| Kdo jsou SEO konkurenti (domény) | `website_serp_competition_domains` |
| Které stránky konkurence přebít | `website_serp_competition_pages` |
| O čem psát dál (gap vs konkurenti) | `website_content_gap` |
| Kde si web kanibalizuje slova | `website_cannibalization` |
