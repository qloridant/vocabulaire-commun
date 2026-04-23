# Tutoriel 03 — Localiser avec Core Location Vocabulary

> **Objectif** : Décrire des adresses et des géométries avec le Core Location Vocabulary.  
> **Durée estimée** : 25 minutes  
> **Niveau** : Débutant

---

## Ce que vous allez apprendre

- Comment modéliser une adresse avec `locn:Address`
- Comment ajouter des coordonnées géographiques
- Comment s'aligner avec la BAN (Base Adresse Nationale)

---

## 1. Structure de Core Location

Namespace : `http://www.w3.org/ns/locn#`

Deux concepts clés :

| Classe / Propriété | Rôle |
|---|---|
| `locn:Address` | Adresse postale structurée |
| `locn:Geometry` | Représentation géométrique (point, polygone…) |
| `locn:location` | Propriété reliant une ressource à sa localisation |
| `locn:address` | Propriété reliant une ressource à son adresse |

---

## 2. Exemple — Adresse complète

```turtle
@prefix locn:   <http://www.w3.org/ns/locn#> .
@prefix schema: <http://schema.org/> .
@prefix xsd:    <http://www.w3.org/2001/XMLSchema#> .

<https://data.example.gouv.fr/adresse/mairie-paris-4>
    a locn:Address ;

    locn:fullAddress         "2 Place du Parvis Notre-Dame, 75004 Paris, France"@fr ;
    locn:thoroughfare        "Place du Parvis Notre-Dame" ;
    locn:locatorDesignator   "2" ;
    locn:postCode            "75004" ;
    locn:postName            "Paris"@fr ;
    locn:adminUnitL1         "France"@fr ;
    locn:adminUnitL2         "Île-de-France"@fr .
```

---

## 3. Ajouter des coordonnées géographiques

```turtle
@prefix locn:  <http://www.w3.org/ns/locn#> .
@prefix geo:   <http://www.w3.org/2003/01/geo/wgs84_pos#> .
@prefix geosparql: <http://www.opengis.net/ont/geosparql#> .

<https://data.example.gouv.fr/lieu/mairie-paris-4>
    locn:geometry [
        a locn:Geometry ;
        # Format WKT (OGC GeoSPARQL)
        geosparql:asWKT "POINT(2.3508 48.8534)"^^geosparql:wktLiteral ;
        # Ou format latitude/longitude simple
        geo:lat  "48.8534"^^xsd:decimal ;
        geo:long "2.3508"^^xsd:decimal
    ] .
```

---

## 4. Aligner avec la Base Adresse Nationale

La **BAN** expose ses adresses en JSON-LD. Liez-y vos ressources :

```turtle
@prefix owl: <http://www.w3.org/2002/07/owl#> .

<https://data.example.gouv.fr/adresse/mairie-paris-4>
    owl:sameAs <https://adresse.data.gouv.fr/base-adresse-nationale/75056_1079_00002> .
```

> **Conseil** : l'API BAN est disponible sur `https://api-adresse.data.gouv.fr`. Elle retourne du GeoJSON enrichi, convertible en RDF.

---

## Exemple complet — Organisme + Adresse

```turtle
@prefix legal: <http://www.w3.org/ns/legal#> .
@prefix locn:  <http://www.w3.org/ns/locn#> .

<https://data.example.gouv.fr/org/mairie-paris>
    a legal:LegalEntity ;
    legal:legalName      "Ville de Paris"@fr ;
    legal:registeredAddress <https://data.example.gouv.fr/adresse/mairie-paris-4> .

<https://data.example.gouv.fr/adresse/mairie-paris-4>
    a locn:Address ;
    locn:fullAddress "Hôtel de Ville, 75196 Paris Cedex 04, France"@fr ;
    locn:postCode    "75196" ;
    locn:postName    "Paris"@fr .
```

---

## Étape suivante

→ [Tutoriel 04 — Décrire un service public avec CPSV](./04-core-public-service.md)
