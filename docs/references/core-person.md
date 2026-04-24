# Référence — Core Person Vocabulary

**Namespace** : `http://www.w3.org/ns/person#`  
**Préfixe courant** : `person:`  
**Spécification** : [https://semiceu.github.io/Core-Person-Vocabulary/](https://semiceu.github.io/Core-Person-Vocabulary/)  
**Mainteneur** : W3C / Commission européenne (SEMIC)

---

## Classes

### `person:Person`

Représente une personne physique dans un contexte administratif.

```turtle
<https://data.example.gouv.fr/personne/p-001>
    a person:Person .
```

---

## Propriétés

### Identité

| Propriété | Type | Description |
|---|---|---|
| `person:birthName` | `rdfs:Literal` | Nom de naissance (avant modification légale) |
| `person:patronymicName` | `rdfs:Literal` | Nom patronymique |
| `person:matronymicName` | `rdfs:Literal` | Nom matronymique |

> Les propriétés `schema:givenName`, `schema:familyName`, `schema:birthDate` de **schema.org** sont recommandées en complément.

### Localisation

| Propriété | Type | Description |
|---|---|---|
| `person:placeOfBirth` | `dct:Location` | Lieu de naissance |
| `person:placeOfDeath` | `dct:Location` | Lieu de décès |
| `person:countryOfBirth` | `dct:Location` | Pays de naissance |
| `person:countryOfDeath` | `dct:Location` | Pays de décès |
| `person:citizenship` | `dct:Location` | Nationalité |
| `person:residency` | `dct:Location` | Lieu de résidence |

### Identité de genre

| Propriété | Type | Description |
|---|---|---|
| `person:gender` | `skos:Concept` | Genre (valeur issue d'un référentiel contrôlé) |

---

## Exemple complet

```turtle
@prefix person:  <http://www.w3.org/ns/person#> .
@prefix schema:  <http://schema.org/> .
@prefix dct:     <http://purl.org/dc/terms/> .
@prefix xsd:     <http://www.w3.org/2001/XMLSchema#> .

<https://data.example.gouv.fr/personne/p-001>
    a person:Person ;

    # Identité principale
    schema:givenName       "Jean" ;
    schema:familyName      "Martin" ;
    person:birthName       "Jean Martin" ;
    schema:birthDate       "1972-09-03"^^xsd:date ;

    # Localisation
    person:countryOfBirth  <http://publications.europa.eu/resource/authority/country/FRA> ;
    person:placeOfBirth    <https://data.example.gouv.fr/commune/75056> ;
    person:citizenship     <http://publications.europa.eu/resource/authority/country/FRA> ;

    # Genre (référentiel EU)
    person:gender          <http://publications.europa.eu/resource/authority/human-sex/MALE> .
```

---

## Propriétés à ne pas utiliser

| Propriété | Raison |
|---|---|
| `foaf:name` | Trop générique, non structuré |
| Champ texte libre pour la date | Utiliser `xsd:date` obligatoirement |

---

## Compatibilité

Ce vocabulaire est compatible et utilisé conjointement avec :

- **schema.org** — pour les propriétés génériques
- **Core Location Vocabulary** — pour les adresses
- **W3C Org Ontology** — pour les affiliations organisationnelles
- **DCAT-AP** — pour la description de jeux de données contenant des personnes

---

## Voir aussi

- [Tutoriel 01 — Mon premier triplet RDF avec Core Person](../tutoriels/01-core-person.md)
- [Core Business Vocabulary](./core-business.md)
- [Core Location Vocabulary](./core-location.md)
