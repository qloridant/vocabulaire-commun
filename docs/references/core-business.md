# Référence — Core Business Vocabulary

**Namespace** : `http://www.w3.org/ns/legal#`  
**Préfixe courant** : `legal:`  
**Spécification** : [https://semiceu.github.io/Core-Business-Vocabulary/](https://semiceu.github.io/Core-Business-Vocabulary/)  
**Mainteneur** : W3C / Commission européenne (SEMIC)

---

## Classes

### `legal:LegalEntity`

Toute personne morale reconnue par un système juridique : entreprise, association, administration, établissement public.

```turtle
<https://data.example.gouv.fr/org/dinum>
    a legal:LegalEntity .
```

---

## Propriétés

### Identification

| Propriété | Type | Description |
|---|---|---|
| `legal:legalName` | `rdfs:Literal` | Dénomination légale officielle |
| `legal:alternativeName` | `rdfs:Literal` | Nom usuel ou abrégé |
| `legal:legalIdentifier` | `rdfs:Literal` | Identifiant légal (SIRET, RNA…) |
| `legal:companyStatus` | `skos:Concept` | Statut (actif, dissous…) |
| `legal:companyType` | `skos:Concept` | Type (SA, association, établissement public…) |

### Localisation

| Propriété | Type | Description |
|---|---|---|
| `legal:registeredAddress` | `locn:Address` | Siège social / adresse officielle |

---

## Exemple — Administration centrale

```turtle
@prefix legal:  <http://www.w3.org/ns/legal#> .
@prefix org:    <http://www.w3.org/ns/org#> .
@prefix schema: <http://schema.org/> .
@prefix locn:   <http://www.w3.org/ns/locn#> .
@prefix dct:    <http://purl.org/dc/terms/> .

<https://data.example.gouv.fr/org/dinum>
    a legal:LegalEntity, org:Organization ;

    legal:legalName          "Direction interministérielle du numérique"@fr ;
    legal:alternativeName    "DINUM"@fr ;
    legal:legalIdentifier    "13001518700013" ;          # SIRET
    legal:companyType
        <http://publications.europa.eu/resource/authority/legal-entity-type/GOV> ;

    schema:url               <https://www.numerique.gouv.fr> ;
    schema:foundingDate      "2019-01-01" ;

    legal:registeredAddress  <https://data.example.gouv.fr/adresse/dinum> ;

    org:subOrganizationOf    <https://data.example.gouv.fr/org/dge> .
```

---

## Exemple — Établissement public

```turtle
@prefix legal:  <http://www.w3.org/ns/legal#> .

<https://data.example.gouv.fr/org/anct>
    a legal:LegalEntity ;
    legal:legalName       "Agence nationale de la cohésion des territoires"@fr ;
    legal:alternativeName "ANCT"@fr ;
    legal:companyType
        <http://publications.europa.eu/resource/authority/legal-entity-type/EPNA> ;
    legal:legalIdentifier "130025619" .       # SIREN
```

---

## Utilisation avec W3C Org Ontology

Le Core Business est souvent combiné avec l'**Organisation Ontology** (W3C) pour les structures hiérarchiques :

```turtle
@prefix org:   <http://www.w3.org/ns/org#> .
@prefix legal: <http://www.w3.org/ns/legal#> .

<https://data.example.gouv.fr/org/bureau-si>
    a org:OrganizationalUnit ;
    org:unitOf             <https://data.example.gouv.fr/org/dinum> ;
    org:headOf             <https://data.example.gouv.fr/personne/chef-bureau> ;
    legal:legalName        "Bureau des Systèmes d'Information"@fr .
```

---

## Types d'organisations (référentiel EU)

| Code | Label |
|---|---|
| `GOV` | Organisme gouvernemental |
| `EPNA` | Établissement public national |
| `PUBL` | Organisme public |
| `PRIV` | Entité privée |

URL de base : `http://publications.europa.eu/resource/authority/legal-entity-type/`

---

## Voir aussi

- [Tutoriel 02 — Décrire une organisation](../tutoriels/02-core-business.md)
- [Core Location Vocabulary](./core-location.md)
- [W3C Organization Ontology](https://www.w3.org/TR/vocab-org/)
