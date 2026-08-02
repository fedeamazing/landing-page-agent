---
description: Lancia il seo-agent — pacchetto SEO on-page (title, meta, dati strutturati, sitemap/robots), condizionale alla sorgente di traffico
argument-hint: [progetto o pagina]
---

# /seo

Avvia il `seo-agent` (Fase 6b, condizionale). Step indipendente: va dopo `/copy` e prima di `/build`, ma è richiamabile in qualsiasi momento — anche su un progetto già buildato, per un audit o una patch SEO.

Input: $ARGUMENTS

## Cosa fare
1. Verifica se serve: sorgente traffico nel brief include SEO organico, o l'utente lo chiede esplicitamente. Se il progetto è 100% paid traffic e nessuno lo ha chiesto, chiedi conferma prima di procedere — potrebbe essere lavoro non necessario.
2. Leggi copy approvato, brief, wireframe, `PRODUCT.md`, `DESIGN.md`.
3. Applica `directives/14_seo_tecnica.md` per generare il pacchetto: title, meta description, slug, check H1, OG/Twitter, JSON-LD, sitemap.xml, robots.txt.
4. Output: `output/<slug>/seo/seo_pack.md`.
5. Se il progetto è già in `output/<slug>/build/`, offri di incorporare subito i tag nel codice; altrimenti se ne occupa `/build` alla fase successiva.
