# Tutoriel 04 — Pré-remplissage d'une démarche avec CPSV-AP et validation SHACL

> **Objectif** : Comprendre comment des données usager transmises pour le pré-remplissage d'une démarche administrative peuvent être structurées avec CPSV-AP, identifier les erreurs du JSON-LD initial, et le corriger pour passer la validation SHACL officielle.  
> **Durée estimée** : 45 minutes  
> **Niveau** : Intermédiaire

---

## Ce que vous allez apprendre

- Comment CPSV-AP structure une démarche et ses données usager (`cv:isAbout`)
- Comment valider un JSON-LD contre les règles SHACL officielles de CPSV-AP 3.2.0
- Identifier et corriger des erreurs réelles détectées par le validateur

---

## 1. Le contexte : pré-remplissage d'une démarche

Lorsqu'un usager initie une démarche en ligne (par exemple via FranceConnect), le système peut transmettre les données déjà connues de l'usager au formulaire cible. Cette transmission peut être structurée en JSON-LD avec CPSV-AP.

Le service décrit ici est l'**Opération Tranquillité Vacances** — une démarche permettant à un particulier de signaler son absence à la police ou à la gendarmerie.

---

## 2. L'exemple initial

Voici le JSON-LD transmis par le système :

!!! warning
    L'exemple suivant contient volontairement des erreurs ! La version corrigée est plus bas.

```json
{
  "@context": {
    "cpsv": "http://purl.org/vocab/cpsv#",
    "cv":   "http://data.europa.eu/m8g/",
    "foaf": "http://xmlns.com/foaf/0.1/",
    "locn": "http://www.w3.org/ns/locn#",
    "dct":  "http://purl.org/dc/terms/"
  },
  "@id":   "https://demarches.service-public.gouv.fr/mademarche/demarcheGenerique/?codeDemarche=OperationTranquilliteVacances",
  "@type": "cpsv:PublicService",
  "dct:description": {
    "@language": "fr",
    "@value": "Démarche en ligne pour le service Opération Tranquilité Vacances"
  },
  "cv:hasCompetentAuthority": {
    "@id":   "https://api.monservice.fr/organisations/dila",
    "@type": "foaf:Organization",
    "foaf:name":     "DILA",
    "foaf:nickname": "DILA",
    "foaf:mbox":     { "@id": "mailto:contact@dila.gouv.fr" },
    "foaf:homepage": { "@id": "https://www.services-publics.gouv.fr" }
  },
  "cv:isAbout": {
    "@type":           "foaf:Person",
    "foaf:familyName": "Wossewodda",
    "foaf:mbox":       { "@id": "mailto:wossewodda-3728@yopmail.com" },
    "locn:address": {
      "@type":             "locn:Address",
      "locn:thoroughfare": "20 Avenue de Ségur",
      "locn:postName":     "Paris",
      "locn:postCode":     "75007"
    }
  }
}
```

---

## 3. Valider avec les shapes SHACL officielles

Les règles de validation de CPSV-AP 3.2.0 sont publiées par la Commission européenne :

```
https://semiceu.github.io/CPSV-AP/releases/3.2.0/shacl/cpsv-ap-SHACL.ttl
```

### Installation de pyshacl

```bash
pip install pyshacl rdflib
```

### Script de validation

```python
from pyshacl import validate
from rdflib import Graph

# Charger les données à valider
data_graph = Graph()
data_graph.parse("exemple.jsonld", format="json-ld")

# Charger les shapes SHACL
shacl_graph = Graph()
shacl_graph.parse("cpsv-ap-SHACL.ttl", format="turtle")

# Lancer la validation
conforms, results_graph, results_text = validate(
    data_graph,
    shacl_graph=shacl_graph,
    inference='rdfs',       # Active le raisonnement RDFS
    abort_on_first=False,   # Récupère toutes les erreurs
    allow_infos=True,
)

print(f"Conforme : {conforms}")
print(results_text)
```

---

## 4. Rapport de validation — 5 violations détectées

L'exemple initial produit le rapport suivant :

```
Validation Report
Conforms: False
Results (5):
```

Voici chaque violation expliquée :

---

### ❌ Violation 1 — `cv:hasCompetentAuthority` : mauvais type

**Règle SHACL** : `PublicServiceShape` — `hasCompetentAuthority` doit pointer vers une instance de `cv:PublicOrganisation`.

**Erreur** :
```
Value does not have class <http://data.europa.eu/m8g/PublicOrganisation>
Focus Node: ...OperationTranquilliteVacances
Value Node: https://api.monservice.fr/organisations/dila
```

**Cause** : L'organisme est typé `foaf:Organization` alors que CPSV-AP exige `cv:PublicOrganisation`.

**Correction** :
```json
"cv:hasCompetentAuthority": {
  "@id":   "https://api.monservice.fr/organisations/dila",
  "@type": "cv:PublicOrganisation"
}
```

---

### ❌ Violation 2 — `dct:title` absent (`name` obligatoire)

**Règle SHACL** : `PublicServiceShape` — `dct:title` est obligatoire (`minCount 1`).

**Erreur** :
```
Less than 1 values on ...OperationTranquilliteVacances -> dct:title
```

**Cause** : L'exemple fournit `dct:description` mais omet `dct:title`.

**Correction** :
```json
"dct:title": {
  "@language": "fr",
  "@value": "Opération Tranquillité Vacances"
}
```

---

### ❌ Violation 3 — `dct:identifier` absent

**Règle SHACL** : `PublicServiceShape` — `dct:identifier` est obligatoire (`minCount 1`).

**Erreur** :
```
Less than 1 values on ...OperationTranquilliteVacances -> dct:identifier
```

**Cause** : L'identifiant métier de la démarche n'est pas déclaré. L'URI de la ressource ne suffit pas.

**Correction** :
```json
"dct:identifier": "OperationTranquilliteVacances"
```

---

### ❌ Violation 4 — `locn:thoroughfare` sans balise de langue

**Règle SHACL** : `AddressShape` — `locn:thoroughfare` doit avoir le datatype `rdf:langString`.

**Erreur** :
```
Value is not Literal with datatype rdf:langString
Value Node: Literal("20 Avenue de Ségur")
```

**Cause** : La valeur est un littéral simple. SHACL exige un littéral avec langue (`@language`).

**Correction** :
```json
"locn:thoroughfare": { "@language": "fr", "@value": "20 Avenue de Ségur" }
```

---

### ❌ Violation 5 — `locn:postName` sans balise de langue

Même cause que la violation 4, pour `locn:postName`.

**Correction** :
```json
"locn:postName": { "@language": "fr", "@value": "Paris" }
```

---

## 5. Le JSON-LD corrigé et conforme

```json
{
  "@context": {
    "cpsv":   "http://purl.org/vocab/cpsv#",
    "cv":     "http://data.europa.eu/m8g/",
    "dct":    "http://purl.org/dc/terms/",
    "skos":   "http://www.w3.org/2004/02/skos/core#",
    "locn":   "http://www.w3.org/ns/locn#",
    "foaf":   "http://xmlns.com/foaf/0.1/"
  },
  "@id":   "https://demarches.service-public.gouv.fr/mademarche/demarcheGenerique/?codeDemarche=OperationTranquilliteVacances",
  "@type": "cpsv:PublicService",

  "dct:title": {
    "@language": "fr",
    "@value": "Opération Tranquillité Vacances"
  },
  "dct:description": {
    "@language": "fr",
    "@value": "Démarche en ligne pour le service Opération Tranquillité Vacances"
  },
  "dct:identifier": "OperationTranquilliteVacances",

  "cv:hasCompetentAuthority": {
    "@id":   "https://api.monservice.fr/organisations/dila",
    "@type": "cv:PublicOrganisation",

    "skos:prefLabel": {
      "@language": "fr",
      "@value": "Direction de l'information légale et administrative"
    },
    "dct:identifier": "13002526900013",

    "dct:spatial": {
      "@id":   "http://publications.europa.eu/resource/authority/country/FRA",
      "@type": "dct:Location"
    },

    "foaf:homepage": { "@id": "https://www.service-public.fr" }
  },

  "cv:isAbout": {
    "@id":   "https://demarches.service-public.gouv.fr/usager/wossewodda",
    "@type": "foaf:Person",
    "foaf:familyName": "Wossewodda",
    "foaf:mbox": { "@id": "mailto:wossewodda-3728@yopmail.com" },
    "locn:address": {
      "@type":             "locn:Address",
      "locn:thoroughfare": { "@language": "fr", "@value": "20 Avenue de Ségur" },
      "locn:postName":     { "@language": "fr", "@value": "Paris" },
      "locn:postCode":     "75007"
    }
  }
}
```

Résultat de validation :

```
Validation Report
Conforms: True
```

---

## 6. Récapitulatif des corrections

| # | Propriété | Problème | Correction |
|---|---|---|---|
| 1 | `cv:hasCompetentAuthority` | Type `foaf:Organization` au lieu de `cv:PublicOrganisation` | Changer le `@type` |
| 2 | `dct:title` | Absent — obligatoire (`minCount 1`) | Ajouter le titre en `rdf:langString` |
| 3 | `dct:identifier` | Absent — obligatoire (`minCount 1`) | Ajouter l'identifiant métier |
| 4 | `locn:thoroughfare` | Littéral simple au lieu de `rdf:langString` | Ajouter `@language` |
| 5 | `locn:postName` | Même problème | Ajouter `@language` |

Et deux ajouts nécessaires sur `cv:PublicOrganisation` :

| Propriété | Obligation | Valeur ajoutée |
|---|---|---|
| `skos:prefLabel` | `minCount 1` | Nom officiel en `rdf:langString` |
| `dct:spatial` | `minCount 1` | URI pays typé `dct:Location` |
| `dct:identifier` | `minCount 1` | SIRET de l'organisme |

---

## 7. Points de vigilance généraux

!!! warning "Les littéraux de texte dans CPSV-AP"
    La grande majorité des propriétés textuelles de CPSV-AP ont le datatype `rdf:langString`, c'est-à-dire qu'elles **exigent une balise de langue** (`@language` en JSON-LD). Un littéral simple `"Paris"` n'est pas équivalent à `{"@language": "fr", "@value": "Paris"}` pour le validateur SHACL.

!!! tip "`cv:PublicOrganisation` ≠ `foaf:Organization`"
    `cv:PublicOrganisation` est la classe spécifique de CPSV-AP pour les organismes publics. Elle impose `skos:prefLabel`, `dct:identifier` et `dct:spatial` (obligatoires). `foaf:Organization` est une classe générique qui n'a pas ces contraintes — elle ne satisfait pas la shape SHACL de CPSV-AP.

!!! tip "L'URI de la ressource ne remplace pas `dct:identifier`"
    Même si votre service a une URI pérenne, CPSV-AP exige un `dct:identifier` explicite — typiquement le code métier de la démarche ou le SIRET de l'organisme.

---

## Ressources

- [Spécification CPSV-AP 3.2.0](https://semiceu.github.io/CPSV-AP/releases/3.2.0/)
- [Shapes SHACL CPSV-AP 3.2.0](https://semiceu.github.io/CPSV-AP/releases/3.2.0/shacl/cpsv-ap-SHACL.ttl)
- [Référence interne — CPSV-AP](../references/core-public-service.md)
- [pyshacl — documentation](https://github.com/RDFLib/pySHACL)
- [JSON-LD Playground](https://json-ld.org/playground/) — pour visualiser les triplets générés