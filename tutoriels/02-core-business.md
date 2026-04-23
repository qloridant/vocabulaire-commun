# Tutoriel 02 — Décrire une organisation avec Core Business

> **Objectif** : Publier les données d'un organisme public en RDF en utilisant le Core Business Vocabulary.  
> **Durée estimée** : 30 minutes  
> **Niveau** : Débutant

---

## Ce que vous allez apprendre

- La structure du **Core Business Vocabulary**
- Comment décrire une administration ou un établissement public
- Comment lier votre organisation à des référentiels français existants

---

## 1. Rappel — Qu'est-ce que Core Business ?

Le **Core Business Vocabulary** (CBV) décrit les **personnes morales** : entreprises, associations, organismes publics. Il distingue :

| Classe | Usage |
|---|---|
| `legal:LegalEntity` | Toute personne morale |
| `org:Organization` | Organisation (W3C Org Ontology, complémentaire) |

Namespace : `http://www.w3.org/ns/legal#`

---

## 2. Exemple — Une préfecture

```turtle
@prefix legal:  <http://www.w3.org/ns/legal#> .
@prefix org:    <http://www.w3.org/ns/org#> .
@prefix schema: <http://schema.org/> .
@prefix locn:   <http://www.w3.org/ns/locn#> .

<https://data.example.gouv.fr/org/prefecture-69>
    a legal:LegalEntity, org:Organization ;

    # Identification
    legal:legalName          "Préfecture du Rhône"@fr ;
    legal:companyType        <http://publications.europa.eu/resource/authority/legal-entity-type/GOV> ;

    # Identifiant SIRET (via schema.org)
    schema:identifier        "13000548700010" ;

    # Site web
    schema:url               <https://www.rhone.gouv.fr> ;

    # Adresse (Core Location)
    legal:registeredAddress  <https://data.example.gouv.fr/adresse/prefecture-69> .
```

---

## 3. Lier à des référentiels français

Enrichissez vos données en pointant vers des sources officielles :

```turtle
@prefix owl:    <http://www.w3.org/2002/07/owl#> .
@prefix skos:   <http://www.w3.org/2004/02/skos/core#> .

<https://data.example.gouv.fr/org/prefecture-69>
    # Alignement avec data.gouv.fr
    owl:sameAs <https://www.data.gouv.fr/fr/organizations/prefecture-du-rhone/> ;

    # Code officiel géographique
    schema:identifier [
        a schema:PropertyValue ;
        schema:propertyID "SIREN" ;
        schema:value "130005487"
    ] .
```

> **Bonne pratique** : utilisez `owl:sameAs` pour relier votre URI à d'autres identifiants officiels (SIRET, SIREN, DUNS).

---

## 4. Structurer une hiérarchie d'organisations

```turtle
@prefix org: <http://www.w3.org/ns/org#> .

# Unité administrative enfant
<https://data.example.gouv.fr/org/bureau-etrangers-69>
    a org:OrganizationalUnit ;
    org:unitOf <https://data.example.gouv.fr/org/prefecture-69> ;
    legal:legalName "Bureau des étrangers — Rhône"@fr .
```

---

## Vérifiez votre compréhension

- [ ] Quelle est la différence entre `legal:LegalEntity` et `org:Organization` ?
- [ ] Où placer le numéro SIRET dans le graphe ?
- [ ] Comment exprimer qu'un bureau dépend d'une préfecture ?

---

## Étape suivante

→ [Tutoriel 03 — Localiser avec Core Location](./03-core-location.md)
