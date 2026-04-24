# Tutoriel 03 — Localiser avec Core Location Vocabulary en JSON-LD

> **Objectif** : Décrire des adresses et des géométries avec le Core Location Vocabulary, directement en JSON-LD.  
> **Durée estimée** : 30 minutes  
> **Niveau** : Débutant

---

## Ce que vous allez apprendre

- Comment modéliser une adresse avec `locn:Address` en JSON-LD
- Comment ajouter des coordonnées géographiques
- Comment lier une adresse à une organisation
- Comment s'aligner avec la BAN (Base Adresse Nationale)

---

## 1. Structure de Core Location

Namespace : `http://www.w3.org/ns/locn#`

| Classe / Propriété | Rôle |
|---|---|
| `locn:Address` | Adresse postale structurée |
| `locn:Geometry` | Représentation géométrique (point, polygone…) |
| `locn:location` | Propriété reliant une ressource à sa localisation |
| `locn:address` | Propriété reliant une ressource à son adresse |

---

## 2. Déclarer le contexte JSON-LD

Avant de décrire une adresse, déclarez les préfixes dans le `@context` :

```json
{
  "@context": {
    "locn":      "http://www.w3.org/ns/locn#",
    "legal":     "http://www.w3.org/ns/legal#",
    "geo":       "http://www.w3.org/2003/01/geo/wgs84_pos#",
    "geosparql": "http://www.opengis.net/ont/geosparql#",
    "owl":       "http://www.w3.org/2002/07/owl#",
    "xsd":       "http://www.w3.org/2001/XMLSchema#"
  }
}
```

---

## 3. Exemple — Adresse complète

```json
{
  "@context": {
    "locn": "http://www.w3.org/ns/locn#"
  },
  "@id":   "https://data.example.gouv.fr/adresse/mairie-paris-4",
  "@type": "locn:Address",

  "locn:fullAddress":       { "@language": "fr", "@value": "2 Place du Parvis Notre-Dame, 75004 Paris, France" },
  "locn:thoroughfare":      { "@language": "fr", "@value": "Place du Parvis Notre-Dame" },
  "locn:locatorDesignator": "2",
  "locn:postCode":          "75004",
  "locn:postName":          { "@language": "fr", "@value": "Paris" },
  "locn:adminUnitL1":       { "@language": "fr", "@value": "France" },
  "locn:adminUnitL2":       { "@language": "fr", "@value": "Île-de-France" }
}
```

!!! warning "Balises de langue obligatoires"
    Les propriétés textuelles de `locn:Address` (`thoroughfare`, `postName`, `fullAddress`…) attendent un `rdf:langString`, c'est-à-dire un littéral **avec balise de langue**. Sans `@language`, les validateurs SHACL signalent une violation. Voir le [Tutoriel 04](./04-core-public-service.md) pour un exemple concret.

---

## 4. Ajouter des coordonnées géographiques

```json
{
  "@context": {
    "locn":      "http://www.w3.org/ns/locn#",
    "geo":       "http://www.w3.org/2003/01/geo/wgs84_pos#",
    "geosparql": "http://www.opengis.net/ont/geosparql#",
    "xsd":       "http://www.w3.org/2001/XMLSchema#",
    "wktLiteral": { "@id": "http://www.opengis.net/ont/geosparql#wktLiteral", "@type": "@id" }
  },
  "@id":   "https://data.example.gouv.fr/lieu/mairie-paris-4",
  "@type": "http://purl.org/dc/terms/Location",

  "locn:geometry": {
    "@type": "locn:Geometry",
    "geosparql:asWKT": {
      "@type":  "geosparql:wktLiteral",
      "@value": "POINT(2.3508 48.8534)"
    },
    "geo:lat":  { "@type": "xsd:decimal", "@value": "48.8534" },
    "geo:long": { "@type": "xsd:decimal", "@value": "2.3508" }
  }
}
```

---

## 5. Exemple complet — Organisation avec adresse et coordonnées

```json
{
  "@context": {
    "locn":      "http://www.w3.org/ns/locn#",
    "legal":     "http://www.w3.org/ns/legal#",
    "geo":       "http://www.w3.org/2003/01/geo/wgs84_pos#",
    "geosparql": "http://www.opengis.net/ont/geosparql#",
    "owl":       "http://www.w3.org/2002/07/owl#",
    "xsd":       "http://www.w3.org/2001/XMLSchema#"
  },
  "@id":   "https://data.example.gouv.fr/org/mairie-paris",
  "@type": "legal:LegalEntity",

  "legal:legalName": { "@language": "fr", "@value": "Ville de Paris" },

  "legal:registeredAddress": {
    "@id":   "https://data.example.gouv.fr/adresse/mairie-paris-4",
    "@type": "locn:Address",

    "locn:fullAddress":  { "@language": "fr", "@value": "Hôtel de Ville, 75196 Paris Cedex 04, France" },
    "locn:thoroughfare": { "@language": "fr", "@value": "Place de l'Hôtel de Ville" },
    "locn:postCode":     "75196",
    "locn:postName":     { "@language": "fr", "@value": "Paris" },
    "locn:adminUnitL1":  { "@language": "fr", "@value": "France" },
    "locn:adminUnitL2":  { "@language": "fr", "@value": "Île-de-France" },

    "owl:sameAs": { "@id": "https://adresse.data.gouv.fr/base-adresse-nationale/75056_4489_00001" }
  },

  "locn:location": {
    "@type": "http://purl.org/dc/terms/Location",
    "locn:geometry": {
      "@type": "locn:Geometry",
      "geo:lat":  { "@type": "xsd:decimal", "@value": "48.8566" },
      "geo:long": { "@type": "xsd:decimal", "@value": "2.3522" }
    }
  }
}
```

---

## 6. Aligner avec la Base Adresse Nationale

La **BAN** expose ses adresses via une API GeoJSON. Pour relier votre adresse à la BAN :

```json
{
  "@context": {
    "locn": "http://www.w3.org/ns/locn#",
    "owl":  "http://www.w3.org/2002/07/owl#"
  },
  "@id":   "https://data.example.gouv.fr/adresse/mairie-paris-4",
  "@type": "locn:Address",

  "locn:fullAddress": { "@language": "fr", "@value": "2 Place du Parvis Notre-Dame, 75004 Paris" },

  "owl:sameAs": {
    "@id": "https://adresse.data.gouv.fr/base-adresse-nationale/75056_1079_00002"
  }
}
```

Pour trouver l'identifiant BAN d'une adresse :

```bash
curl "https://api-adresse.data.gouv.fr/search/?q=2+place+du+parvis+notre-dame+paris&limit=1"
```

La réponse GeoJSON contient le champ `id` à utiliser dans `owl:sameAs`.

---

## 7. Valider votre JSON-LD

```bash
# Convertir en Turtle pour vérification visuelle
python3 -c "
from rdflib import Graph
g = Graph()
g.parse('adresse.jsonld', format='json-ld')
print(g.serialize(format='turtle'))
"
```

Ou utilisez le [JSON-LD Playground](https://json-ld.org/playground/) pour visualiser les triplets générés.

---

## Vérifiez votre compréhension

- [ ] Pourquoi `locn:postName` doit-il avoir `@language` et pas `locn:postCode` ?
- [ ] Quelle est la différence entre `locn:address` (propriété) et `locn:Address` (classe) ?
- [ ] Comment retrouver l'identifiant BAN d'une adresse pour l'aligner avec `owl:sameAs` ?

---

## Étape suivante

→ [Tutoriel 04 — Pré-remplissage d'une démarche avec CPSV-AP et validation SHACL](./04-core-public-service.md)

---

## Références

- [Référence interne — Core Location Vocabulary](../references/core-location.md)
- [Spécification W3C — Core Location](https://www.w3.org/ns/locn)
- [API Base Adresse Nationale](https://api-adresse.data.gouv.fr)
- [GeoSPARQL — OGC](https://www.ogc.org/standard/geosparql/)