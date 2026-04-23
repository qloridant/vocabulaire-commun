# Guide — Interroger un triplestore avec SPARQL

> **Problème** : Vous avez des données RDF publiées et souhaitez les interroger de manière expressive.  
> **Solution** : Utiliser SPARQL, le langage de requête du Web sémantique.

---

## Quand utiliser ce guide

- Vous avez un triplestore (Virtuoso, GraphDB, Fuseki…)
- Vous souhaitez extraire, filtrer ou agréger des données RDF
- Vous voulez fédérer des requêtes sur plusieurs sources

---

## 1. Structure d'une requête SPARQL de base

```sparql
PREFIX legal: <http://www.w3.org/ns/legal#>
PREFIX schema: <http://schema.org/>

SELECT ?nom ?siret
WHERE {
  ?org a legal:LegalEntity ;
       legal:legalName ?nom ;
       schema:identifier ?siret .
}
LIMIT 10
```

---

## 2. Filtrer par valeur

```sparql
PREFIX legal: <http://www.w3.org/ns/legal#>
PREFIX locn:  <http://www.w3.org/ns/locn#>

SELECT ?org ?nom ?cp
WHERE {
  ?org a legal:LegalEntity ;
       legal:legalName ?nom ;
       legal:registeredAddress ?adresse .
  ?adresse locn:postCode ?cp .
  FILTER(STRSTARTS(?cp, "75"))   # Uniquement Paris (75xxx)
}
ORDER BY ?nom
```

---

## 3. Requête sur un SPARQL endpoint distant (requête fédérée)

Interroger **data.europa.eu** depuis votre propre endpoint :

```sparql
PREFIX org:  <http://www.w3.org/ns/org#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>

SELECT ?label
WHERE {
  SERVICE <https://publications.europa.eu/webapi/rdf/sparql> {
    <http://publications.europa.eu/resource/authority/country/FRA>
        skos:prefLabel ?label .
    FILTER(LANG(?label) = "fr")
  }
}
```

> **Note** : la requête fédérée (`SERVICE`) permet de combiner vos données locales avec des référentiels européens sans les dupliquer.

---

## 4. Compter et agréger

```sparql
PREFIX legal: <http://www.w3.org/ns/legal#>
PREFIX locn:  <http://www.w3.org/ns/locn#>

SELECT ?departement (COUNT(?org) AS ?nb)
WHERE {
  ?org a legal:LegalEntity ;
       legal:registeredAddress ?adresse .
  ?adresse locn:adminUnitL2 ?departement .
}
GROUP BY ?departement
ORDER BY DESC(?nb)
```

---

## 5. Appel SPARQL depuis Python

```python
import requests

ENDPOINT = "https://data.example.gouv.fr/sparql"

QUERY = """
PREFIX legal: <http://www.w3.org/ns/legal#>
SELECT ?nom WHERE {
  ?org a legal:LegalEntity ;
       legal:legalName ?nom .
}
LIMIT 5
"""

response = requests.get(
    ENDPOINT,
    params={"query": QUERY, "format": "json"},
    headers={"Accept": "application/sparql-results+json"}
)

data = response.json()
for binding in data["results"]["bindings"]:
    print(binding["nom"]["value"])
```

---

## Endpoints SPARQL publics utiles

| Source | URL |
|---|---|
| Publications Office EU | `https://publications.europa.eu/webapi/rdf/sparql` |
| DBpedia | `https://dbpedia.org/sparql` |
| Wikidata | `https://query.wikidata.org/sparql` |
| data.europa.eu | `https://data.europa.eu/sparql` |

---

## Étape suivante

→ [Guide — Aligner ses données avec un référentiel européen](./03-alignement-referentiels.md)
