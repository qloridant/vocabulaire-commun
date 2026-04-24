# Référence — SPARQL 1.1 & Oxigraph

**Oxigraph version** : 0.3.x  
**SPARQL** : 1.1 (W3C)  
**Dépôt** : [https://github.com/oxigraph/oxigraph](https://github.com/oxigraph/oxigraph)  
**Licence** : MIT / Apache 2.0

---

## API HTTP

Oxigraph expose quatre endpoints :

| Endpoint | Méthode | Rôle |
|---|---|---|
| `/query` | GET, POST | Requêtes SPARQL SELECT, CONSTRUCT, ASK, DESCRIBE |
| `/update` | POST | Requêtes SPARQL UPDATE (INSERT, DELETE) |
| `/store` | GET, POST, PUT, DELETE | Gestion du contenu RDF (chargement, export) |
| `/` | GET | Interface web interactive |

---

## `/query` — Interroger les données

### GET

```bash
curl -G http://localhost:7878/query \
  --data-urlencode "query=SELECT ?s ?p ?o WHERE { ?s ?p ?o } LIMIT 5" \
  -H "Accept: application/sparql-results+json"
```

### POST (recommandé pour les longues requêtes)

```bash
curl -X POST http://localhost:7878/query \
  -H "Content-Type: application/sparql-query" \
  -H "Accept: application/sparql-results+json" \
  --data "SELECT ?s ?p ?o WHERE { ?s ?p ?o } LIMIT 5"
```

### Formats de réponse acceptés (`Accept`)

| Format | Media type |
|---|---|
| JSON | `application/sparql-results+json` |
| XML | `application/sparql-results+xml` |
| CSV | `text/csv` |
| TSV | `text/tab-separated-values` |
| Turtle (CONSTRUCT) | `text/turtle` |
| N-Triples (CONSTRUCT) | `application/n-triples` |

---

## `/update` — Modifier les données

```bash
# INSERT
curl -X POST http://localhost:7878/update \
  -H "Content-Type: application/sparql-update" \
  --data "
INSERT DATA {
  <https://data.example.gouv.fr/org/test>
    <http://www.w3.org/ns/legal#legalName> \"Organisation test\"@fr .
}"

# DELETE
curl -X POST http://localhost:7878/update \
  -H "Content-Type: application/sparql-update" \
  --data "
DELETE DATA {
  <https://data.example.gouv.fr/org/test>
    <http://www.w3.org/ns/legal#legalName> \"Organisation test\"@fr .
}"

# DELETE + INSERT (remplacement)
curl -X POST http://localhost:7878/update \
  -H "Content-Type: application/sparql-update" \
  --data "
PREFIX legal: <http://www.w3.org/ns/legal#>
DELETE { <https://data.example.gouv.fr/org/dinum> legal:legalName ?ancien . }
INSERT { <https://data.example.gouv.fr/org/dinum> legal:legalName \"DINUM\"@fr . }
WHERE  { <https://data.example.gouv.fr/org/dinum> legal:legalName ?ancien . }
"
```

---

## `/store` — Gérer le contenu RDF

### Charger un fichier

```bash
# Dans le graphe par défaut
curl -X POST http://localhost:7878/store \
  -H "Content-Type: text/turtle" \
  --data-binary @fichier.ttl

# Dans un graphe nommé
curl -X POST \
  "http://localhost:7878/store?graph=https://data.example.gouv.fr/graph/org" \
  -H "Content-Type: text/turtle" \
  --data-binary @fichier.ttl

# Remplacer entièrement un graphe (PUT)
curl -X PUT \
  "http://localhost:7878/store?graph=https://data.example.gouv.fr/graph/org" \
  -H "Content-Type: text/turtle" \
  --data-binary @fichier.ttl
```

### Exporter les données

```bash
# Exporter tout le store en N-Quads
curl http://localhost:7878/store \
  -H "Accept: application/n-quads" > export.nq

# Exporter un graphe nommé en Turtle
curl "http://localhost:7878/store?graph=https://data.example.gouv.fr/graph/org" \
  -H "Accept: text/turtle" > organisations.ttl
```

### Supprimer un graphe

```bash
curl -X DELETE \
  "http://localhost:7878/store?graph=https://data.example.gouv.fr/graph/org"
```

---

## Formats d'import supportés (`Content-Type`)

| Format | Media type |
|---|---|
| Turtle | `text/turtle` |
| N-Triples | `application/n-triples` |
| N-Quads | `application/n-quads` |
| RDF/XML | `application/rdf+xml` |
| JSON-LD | `application/ld+json` |
| TriG | `application/trig` |

---

## CLI — Commandes principales

```bash
# Démarrer le serveur
oxigraph serve --location ./data --bind 0.0.0.0:7878

# Charger un fichier directement (sans serveur HTTP)
oxigraph load --location ./data --file fichier.ttl

# Charger dans un graphe nommé
oxigraph load --location ./data --file fichier.ttl \
  --graph https://data.example.gouv.fr/graph/org

# Exporter
oxigraph dump --location ./data --output export.nq
```

---

## Requêtes SPARQL 1.1 — Aide-mémoire

```sparql
# SELECT — projeter des variables
SELECT ?nom ?siret
WHERE { ?org legal:legalName ?nom ; legal:legalIdentifier ?siret . }

# OPTIONAL — jointure externe
SELECT ?nom ?url
WHERE {
  ?org legal:legalName ?nom .
  OPTIONAL { ?org schema:url ?url . }
}

# FILTER — filtrer les résultats
FILTER(LANG(?nom) = "fr")
FILTER(STRSTARTS(?siret, "13"))
FILTER(REGEX(?nom, "^Agence", "i"))

# UNION — réunion de patterns
{ ?org a legal:LegalEntity . } UNION { ?org a org:Organization . }

# GROUP BY / HAVING — agrégation
SELECT ?type (COUNT(?org) AS ?nb)
WHERE { ?org a ?type . }
GROUP BY ?type
HAVING (COUNT(?org) > 1)

# CONSTRUCT — produire du RDF
CONSTRUCT { ?org legal:legalName ?nom . }
WHERE { ?org legal:legalName ?nom . FILTER(LANG(?nom) = "fr") }

# ASK — vérifier l'existence
ASK { <https://data.example.gouv.fr/org/dinum> a legal:LegalEntity . }

# SERVICE — requête fédérée
SELECT ?label WHERE {
  SERVICE <https://query.wikidata.org/sparql> {
    wd:Q3286621 rdfs:label ?label .
    FILTER(LANG(?label) = "fr")
  }
}
```

---

## Voir aussi

- [Tutoriel 05 — Charger et interroger avec Oxigraph](../tutoriels/05-oxigraph-local.md)
- [Guide — Déployer Oxigraph en production](../guides/04-oxigraph-deploiement.md)
- [Guide — Requêtes SPARQL](../guides/02-sparql-requetes.md)
- [Spécification SPARQL 1.1 (W3C)](https://www.w3.org/TR/sparql11-overview/)