# Marketing Miner MCP Skill

Komunitní [Agent Skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview)
pro AI asistenty, kteří umí pracovat se Skills (Claude Code, Claude.ai, Claude
Desktop). Naučí model **používat MCP server [Marketing Mineru](https://www.marketingminer.com/)
správně** — vybrat dobrý tool, poslat správné parametry, číst data jako stratég
a šetřit kredity.

Není to nový nástroj. Je to **vrstva chytrosti nad existujícím MCP serverem**:
sebraná zkušenost o tom, kde model nejčastěji chybuje a jak z Marketing Mineru
dostat užitečnou odpověď místo přepisu JSONu.

> [!IMPORTANT]
> **Tohle není oficiální projekt Marketing Mineru.** Je to nezávislý komunitní
> příspěvek od marketéra pro marketéry. Nejsem nijak spojený s Marketing Minerem
> ani jeho provozovatelem. Marketing Miner® a další ochranné známky patří jejich
> vlastníkům. Skill žádná data nesbírá ani neukládá — jen radí modelu, jak mluvit
> s MCP serverem, který si připojíš ty.

---

## Co to konkrétně zlepšuje

Generický prompt „používej Marketing Miner" tohle neumí. Skill je ukotvený ve
**skutečných 11 toolech** serveru a řeší přesně ty věci, na kterých model ujíždí:

- ✅ **`type` / `target` rozhodovací strom** — největší zdroj chyb
  (`subdomain` = celý web · `domain` = jedna subdoména · `exact` = jedna URL ·
  `prefix` = složka). Špatná volba = data o něčem jiném.
- ✅ **Rozliší dva zaměnitelné tooly** — `keyword_suggestions` (rozviň seed) vs.
  `website_suggestions` (slova, na která doména už rankuje — matoucí název).
- ✅ **Správné trhy** — `lang` umí `cs, sk, pl, hu, ro, gb, us`, ne jen CZ/SK.
- ✅ **Footguny** — `competitors` jako JSON-string, nehardcodovat `result_type`,
  cap `limit=100`, `search_volume: null` = slovo zahodit.
- ✅ **Optika čtení dat** — „jak tomu říkáš ty ≠ jak to hledá zákazník",
  poptávka ≠ úspěch, sezónnost, trend.
- ✅ **Kreditová disciplína** — MCP čerpá profilery z placeného tarifu.
- ✅ **Atribuce** — čísla se prezentují jako data z Marketing Mineru, ne jako
  vlastní odhad (vyžaduje to sám MCP).

---

## Předpoklady

1. **Placený tarif Marketing Mineru** s přístupem k MCP (free tarif nestačí —
   dotazy čerpají profilery).
2. **Připojený MCP konektor** Marketing Mineru ve tvém AI asistentovi (remote,
   přes OAuth). Aktuální URL a postup najdeš v nastavení svého MM účtu —
   v době psaní to bylo `https://mcp.marketingminer.com/mcp`.
3. AI asistent, který umí Agent Skills (Claude Code / Claude.ai / Claude Desktop).

---

## Instalace

### Claude Code

```bash
# osobní (všechny projekty)
git clone https://github.com/<tvuj-user>/marketing-miner-mcp-skill.git \
  ~/.claude/skills/marketing-miner

# nebo jen pro jeden projekt
git clone https://github.com/<tvuj-user>/marketing-miner-mcp-skill.git \
  ./.claude/skills/marketing-miner
```

Skill se aktivuje sám, když narazíš na keyword research / SEO úkol nad CZ/SK
trhem, nebo ho vyvolej přímo přes `/marketing-miner`.

### Claude.ai / Claude Desktop

Nahraj obsah repa jako Skill v nastavení (Settings → Capabilities → Skills).
Soubor `SKILL.md` je vstupní bod, `references/` se načte podle potřeby.

### Jakýkoli jiný MCP-capable asistent

Nemáš-li podporu Skills, obsah `SKILL.md` + `references/` funguje i jako **system
prompt / instrukce projektu**. Logika (jak mluvit s MCP) je přenositelná.

---

## Jak to používat

Stačí psát přirozeně. Pár příkladů:

- „Kolik lidí v ČR hledá *veganský protein* a jak se to vyvíjelo za rok?"
- „Chci psát o *běžeckých botách* — jaká podtémata a otázky pokrýt?"
- „Na jaká slova je vidět *konkurent.cz* a my ne?"
- „Jak roste organická návštěvnost *husky.cz* oproti *alza.cz*?"
- „Pereme se na webu sami se sebou o nějaké slovo?"
- „Pojmenovat kategorii — *volnočasová obuv*, nebo *boty do města*?"

Model si vybere správný tool, pošle správné parametry a vrátí čísla + závěr.

> [!NOTE]
> **Co MCP neumí** (→ plná appka Marketing Miner): průběžné sledování pozic,
> AI viditelnost, technické/obsahové audity, dávkové reporty tisíců URL,
> zpětné odkazy. Skill tě v těchhle případech pošle do aplikace, nesnaží se to
> ohnout přes MCP.

---

## Struktura repa

```
marketing-miner-mcp-skill/
├─ SKILL.md                  # vstupní bod: role, optika, hranice, scénáře, pravidla
├─ references/
│  ├─ tools.md               # všech 11 toolů: parametry, výstupy, footguny
│  └─ playbooks.md           # úkol → přesná sekvence toolů + jak číst výstup
├─ LICENSE
└─ README.md
```

---

## Přispívání

Našel jsi další footgun, lepší playbook nebo se změnilo schéma toolu? Sem s tím —
issues a PR vítané. Cíl je jediný: aby model dostával z Marketing Mineru přes MCP
co nejužitečnější odpovědi.

## Licence

MIT — viz [LICENSE](LICENSE).

## Autor

[Jindřich Fáborský](https://www.faborsky.cz) — marketér a vzdělavatel
([AI First](https://aifirst.cz)). Skill vznikl z reálné práce s Marketing Miner
MCP a sdílím ho, ať z něj mají užitek i ostatní marketéři.
