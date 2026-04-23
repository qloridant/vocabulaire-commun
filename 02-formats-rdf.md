# Explication — Les formats RDF : Turtle, JSON-LD, RDF/XML

> Cette page explique les différents formats de sérialisation RDF et quand utiliser chacun.

---

## Tous ces formats expriment la même chose

RDF est un **modèle de données** (des triplets). Ce modèle peut s'écrire dans plusieurs formats. Le choix du format dépend du **contexte d'usage**, pas de la signification.

---

## Turtle — Le format lisible par les humains

**Turtle** (Terse RDF Triple Language) est le format recommandé pour l'écriture et la documentation.

```turtle
@prefix person: <http://www.w3.org/ns/person#> .
@prefix schema: <http://schema.org/> .

<https://data.example.gouv.fr/personne/p-001>
    a person:Person ;
    schema:givenName  "Sophie" ;
    schema:familyName "Lefebvre" .
```

✅ Lisible  
✅ Concis (préfixes réutilisables)  
❌ Non natif pour les APIs web

---

## JSON-LD — L'intégration dans les APIs JSON

**JSON-LD** est du JSON enrichi d'un `@context`. C'est le format idéal pour les APIs web.

```json
{
  "@context": {
    "person": "http://www.w3.org/ns/person#",
    "schema": "http://schema.org/",
    "givenName": "schema:givenName",
    "familyName": "schema:familyName"
  },
  "@type": "person:Person",
  "@id": "https://data.example.gouv.fr/personne/p-001",
  "givenName": "Sophie",
  "familyName": "Lefebvre"
}
```

✅ Compatible avec tous les outils JSON  
✅ Facile à intégrer dans une API existante  
✅ Utilisé par schema.org, data.gouv.fr  
❌ Verbeux si le contexte est répété

---

## RDF/XML — Le format historique

**RDF/XML** est le format original de RDF (1999). Encore présent dans les anciens systèmes.

```xml
<?xml version="1.0"?>
<rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
         xmlns:schema="http://schema.org/"
         xmlns:person="http://www.w3.org/ns/person#">
  <person:Person rdf:about="https://data.example.gouv.fr/personne/p-001">
    <schema:givenName>Sophie</schema:givenName>
    <schema:familyName>Lefebvre</schema:familyName>
  </person:Person>
</rdf:RDF>
```

✅ Largement supporté  
❌ Verbeux et difficile à lire  
⚠️ Préférez Turtle ou JSON-LD pour les nouveaux projets

---

## N-Triples — Le format machine

**N-Triples** est un format ligne par ligne, idéal pour l'import/export en masse.

```
<https://data.example.gouv.fr/personne/p-001> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.w3.org/ns/person#Person> .
<https://data.example.gouv.fr/personne/p-001> <http://schema.org/givenName> "Sophie" .
<https://data.example.gouv.fr/personne/p-001> <http://schema.org/familyName> "Lefebvre" .
```

✅ Simple à parser  
✅ Idéal pour les pipelines ETL  
❌ Illisible pour les humains

---

## Tableau de synthèse

| Format | Extension | Content-Type | Usage recommandé |
|---|---|---|---|
| Turtle | `.ttl` | `text/turtle` | Documentation, développement |
| JSON-LD | `.jsonld` | `application/ld+json` | APIs web, open data |
| RDF/XML | `.rdf` | `application/rdf+xml` | Compatibilité legacy |
| N-Triples | `.nt` | `application/n-triples` | Import/export, pipelines |
| N-Quads | `.nq` | `application/n-quads` | Données multi-graphes |

---

## Négociation de contenu

Un bon serveur RDF répond dans le format demandé par le client (**content negotiation**) :

```http
GET /org/dinum HTTP/1.1
Accept: text/turtle
```

```http
HTTP/1.1 200 OK
Content-Type: text/turtle

@prefix legal: <http://www.w3.org/ns/legal#> .
<https://data.example.gouv.fr/org/dinum>
    a legal:LegalEntity ;
    legal:legalName "Direction interministérielle du numérique"@fr .
```

---

## Voir aussi

- [Guide — Publier ses données en JSON-LD](../guides/01-jsonld-publication.md)
- [Explication — Le Web sémantique](./01-web-semantique.md)
