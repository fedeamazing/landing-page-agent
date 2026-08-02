# 14 — SEO Tecnica & Metadata

Genera il pacchetto SEO on-page di una pagina/sito: tag di ricerca (title, meta description), dati strutturati, Open Graph, sitemap e robots. È una directive **tecnica**, non di copywriting: lavora sul copy già approvato, non lo riscrive.

**Output:** `output/<slug>/seo/seo_pack.md`
**Usata da:** `seo-agent` (Fase 6b, condizionale), `design-build-agent` (Fase 7, per l'embedding nell'head), `qa-agent` (Fase 10, per l'audit)
**Non copre:** performance/Core Web Vitals (→ `07_vercel_guidelines`), alt text generico delle immagini (→ già in `07_vercel_guidelines`, qui solo la variante keyword-aware dove naturale), keyword research approfondita da tool esterni tipo Ahrefs/Semrush (assumi la keyword dal brief o deducila dal contesto, segnalando `[KEYWORD DA VERIFICARE]` se assente), monitoraggio post-lancio (→ Fase 12, Google Search Console).

---

## Quando si attiva
- **Automatico:** se il brief (`01_landing_brief.md`) indica che la sorgente di traffico include SEO organico, anche parzialmente.
- **Su richiesta:** l'utente può chiamare `/seo` in qualsiasi momento, anche su un progetto a traffico paid (es. per una sezione blog/risorse dentro una web app) o per un audit su una pagina già live.
- **Da saltare:** landing pure da Google Ads/Meta senza alcuna componente organica. Non appesantire il lavoro: lì il message match con l'annuncio conta più del ranking.

## Principio guida
In caso di conflitto tra conversione e ranking, **vince la conversione**. Questa directive non tocca l'H1 o il copy scritto dal `copy-agent`: se l'H1 CRO non è ottimale per la keyword, l'ottimizzazione si sposta sul `<title>` — che l'utente vede in SERP, non in pagina — non sull'headline che legge chi è già arrivato sul sito.

---

## 1. Title tag
- **Lunghezza:** 50-60 caratteri (~580px in SERP). Oltre, Google tronca.
- **Formula:** `[Beneficio/keyword primaria] + [differenziatore o città, se local] | [Brand]`
- Il brand va in coda, separato da `|` o `–`, tranne quando il brand stesso è la keyword di ricerca (allora va in testa).
- Una keyword primaria per pagina, mai ripetuta più volte: il keyword stuffing è penalizzante oltre che brutto da leggere.

| Tipo di pagina | Pattern | Esempio |
|---|---|---|
| Home / landing servizio locale | `[Servizio] a [Città] \| [Brand]` | `Manutenzione Impianti Antincendio a Genova \| [Brand]` |
| Pagina servizio specifico | `[Servizio specifico] — [Beneficio] \| [Brand]` | `Ricarica Estintori Certificata — Intervento in 24h \| [Brand]` |
| Prodotto e-commerce | `[Nome prodotto] — [Attributo chiave] \| [Brand]` | `Matrioska Personalizzata con Nome — Fatta a Mano \| [Brand]` |
| Articolo/blog | `[Titolo articolo, keyword in testa]` | `Come Scegliere l'Estintore Giusto per il Tuo Negozio` |

**Da evitare:** title generici ("Home", "Benvenuti"), superlativi non supportati, keyword ripetute, title radicalmente diverso dall'H1 senza un motivo di ricerca preciso.

## 2. Meta description
- **Lunghezza:** 150-160 caratteri.
- Non è un fattore di ranking diretto, ma governa il CTR in SERP: è **copy pubblicitario**, non un riassunto.
- Struttura: `[cosa offri] + [per chi/dove] + [motivo per cliccare o CTA]`.
- Include la keyword naturalmente, una volta sola, mai forzata.

> Esempio (139 caratteri): *"Interventi di manutenzione e ricarica estintori a Genova e provincia. Preventivo gratuito in 24h, tecnici certificati. Richiedi ora."*

## 3. URL slug
- Minuscolo, parole separate da trattino, niente stopword inutili (di, per, il, la…), breve (idealmente sotto i 60 caratteri), contiene la keyword primaria.
- **Buono:** `/manutenzione-estintori-genova`
- **Cattivo:** `/pagina2`, `/manutenzione-e-ricarica-degli-estintori-a-genova-e-provincia-preventivo-gratis`

## 4. Gerarchia heading e crawlability
- **Un solo H1 per pagina.** Verifica che nel copy approvato non ce ne siano due — capita nei build multi-sezione se ogni blocco usa il proprio H1 invece di H2.
- H2/H3 seguono la struttura delle sezioni: non servono a "piazzare keyword", servono a far capire a Google e agli screen reader di cosa parla ogni blocco.
- Se l'H1 CRO non contiene la keyword primaria — succede spesso, un H1 persuasivo tipo *"Smetti di rischiare multe sull'impianto"* converte meglio di uno keyword-first — **non lo riscrivi**. Segnali lo scarto nel pacchetto SEO e assorbi la keyword nel `<title>` e in un H2 vicino all'inizio pagina.

## 5. Open Graph & Twitter Card
Tag minimi per ogni pagina pubblica:

```html
<meta property="og:title" content="[max 60 car, può ripetere il title]" />
<meta property="og:description" content="[max 110 car]" />
<meta property="og:image" content="[1200x630px, <8MB, URL https assoluto]" />
<meta property="og:url" content="[URL canonico assoluto]" />
<meta property="og:type" content="website" />
<meta name="twitter:card" content="summary_large_image" />
```

Se manca un'immagine OG dedicata, segnalalo al `design-build-agent`: va generata nel design system del progetto (non uno screenshot generico della pagina), oppure richiesta all'utente come asset.

## 6. Dati strutturati (JSON-LD)
Scegli **un solo** tipo primario per pagina — quello che rappresenta meglio l'entità:

| Contesto | Tipo schema.org |
|---|---|
| Azienda con sede fisica/area di servizio (edilizia, impiantistica, artigianato, negozi locali) | `LocalBusiness` (o sottotipo più specifico se esiste: `HomeAndConstructionBusiness`, `GeneralContractor`, `ProfessionalService`) |
| Sito aziendale/corporate senza sede al pubblico | `Organization` |
| Prodotto singolo (e-commerce) | `Product` + `Offer` annidato |
| Pagina con domande frequenti | `FAQPage` |
| Articolo/contenuto editoriale | `Article` o `BlogPosting` |
| Mini-site multi-pagina | aggiungi `BreadcrumbList` su ogni pagina interna |

Template `LocalBusiness` — compila solo con dati reali dal contesto/brief, mai inventati:

```json
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "[Nome azienda]",
  "image": "[URL logo o foto sede]",
  "telephone": "[DA VERIFICARE]",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "[DA VERIFICARE]",
    "addressLocality": "[Città]",
    "postalCode": "[DA VERIFICARE]",
    "addressCountry": "IT"
  },
  "geo": {
    "@type": "GeoCoordinates",
    "latitude": "[DA VERIFICARE]",
    "longitude": "[DA VERIFICARE]"
  },
  "areaServed": "[Città/provincia servita]",
  "openingHoursSpecification": "[DA VERIFICARE]",
  "url": "[URL sito]",
  "sameAs": ["[profili social, se esistono]"]
}
```

**Regola non negoziabile:** ogni campo senza fonte certa nel brief/contesto resta `[DA VERIFICARE]`, mai un valore plausibile inventato. È dato pubblico indicizzato da Google — un errore qui è peggio di un errore nel copy.

**Attenzione ai doppioni:** se la piattaforma di destinazione genera già markup da sola (es. Shopify inserisce `Product`/`Organization` di suo via tema o app), verifica prima di aggiungere un secondo blocco JSON-LD conflittuale — meglio integrare i campi mancanti che duplicare.

Valida sempre con il Rich Results Test di Google prima di considerarlo finale (fa parte del check del `qa-agent`).

## 7. Canonical e indicizzazione
- Ogni pagina ha un `<link rel="canonical">` self-referenziante di default.
- `noindex` esplicito su: pagina di grazie/thank-you, area riservata/login, pagine di sistema.
- Un solo dominio canonico (https, con o senza www ma coerente ovunque) — verificalo anche in fase di deploy, non solo qui.

## 8. Sitemap.xml e robots.txt

`sitemap.xml` minimo:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url><loc>https://[dominio]/</loc><priority>1.0</priority></url>
  <url><loc>https://[dominio]/[pagina]</loc><priority>0.8</priority></url>
</urlset>
```

`robots.txt` di base:
```
User-agent: *
Allow: /
Disallow: /area-riservata/
Disallow: /api/
Sitemap: https://[dominio]/sitemap.xml
```

## 9. Local SEO (quando il cliente ha una sede o un'area di servizio)
Caso frequente per progetti di business locali con clientela di zona:
- **Coerenza NAP** (Nome, Indirizzo, Telefono): devono essere identici, carattere per carattere, tra sito, `LocalBusiness` JSON-LD e Google Business Profile del cliente, se esiste ed è citato nel contesto. Le incoerenze NAP confondono l'algoritmo di local ranking.
- Cita città/zona nel copy in modo naturale — dovrebbe già emergere dal message match del brief, non va forzata solo per SEO.
- Se il cliente ha un profilo Google Business attivo, verificane la coerenza con quanto scritto qui, ma non modificarlo: è fuori dal perimetro di questa directive.

## Cosa verifica il qa-agent (rimando)
Title/description nei limiti di lunghezza, un solo H1 per pagina, JSON-LD sintatticamente valido, sitemap.xml e robots.txt raggiungibili una volta live. Il dettaglio dei check vive in `qa-agent.md`, non qui.

---

## Output — formato `output/<slug>/seo/seo_pack.md`

```markdown
# SEO Pack — [progetto]

## [Nome pagina] ([url])
- **Title:** [testo] ([N]/60 car)
- **Meta description:** [testo] ([N]/160 car)
- **Slug:** /[slug]
- **Keyword primaria / intent:** [keyword] — [informativo/transazionale/locale/...]
- **H1 (dal copy):** [testo] — coerenza SEO: [ok / scarto notato + dove si è compensato]
- **OG title/description/image:** [...]
- **JSON-LD:** tipo `[Type]`
\`\`\`json
[blocco compilato]
\`\`\`
- **Canonical:** [url] · **Index:** [index/noindex]

---
[ripeti per ogni pagina]

## sitemap.xml
\`\`\`xml
[...]
\`\`\`

## robots.txt
\`\`\`
[...]
\`\`\`
```

## Regole
1. Mai sacrificare l'H1/il copy di conversione per il ranking: il compromesso si assorbe nel `<title>`, non nell'headline visibile.
2. Mai inventare dati strutturati — NAP, orari, rating, prezzi: `[DA VERIFICARE]` se mancano.
3. Mai duplicare markup che la piattaforma genera già da sola.
4. Mai attivarti su un progetto 100% paid traffic senza che sia stato chiesto esplicitamente.
5. Italiano salvo audience diversa, come le altre directive.
