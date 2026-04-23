# Tutoriel 01 — Mon premier triplet RDF avec Core Person

> **Objectif** : Décrire une personne en RDF/Turtle en utilisant le Core Person Vocabulary de l'UE.  
> **Durée estimée** : 30 minutes  
> **Niveau** : Débutant

---

## Ce que vous allez apprendre

- Ce qu'est un triplet RDF
- Comment utiliser le **Core Person Vocabulary**
- Comment valider votre fichier Turtle

---

## 1. Le triplet RDF : sujet — prédicat — objet

Le Web sémantique repose sur des **triplets** :

```
<sujet> <prédicat> <objet> .
```

Exemple en langage naturel :

> Marie Dupont — a pour prénom — "Marie"

En RDF/Turtle :

```turtle
@prefix person: <http://www.w3.org/ns/person#> .
@prefix schema: <http://schema.org/> .

<https://data.example.gouv.fr/personne/marie-dupont>
    a person:Person ;
    schema:givenName "Marie" ;
    schema:familyName "Dupont" .
```

---

## 2. Ajouter des propriétés Core Person

Le Core Person Vocabulary définit des propriétés standardisées pour décrire une personne physique dans un contexte administratif.

```turtle
@prefix person: <http://www.w3.org/ns/person#> .
@prefix schema: <http://schema.org/> .
@prefix xsd:    <http://www.w3.org/2001/XMLSchema#> .

<https://data.example.gouv.fr/personne/marie-dupont>
    a person:Person ;

    # Identité
    schema:givenName      "Marie" ;
    schema:familyName     "Dupont" ;
    schema:birthDate      "1985-04-12"^^xsd:date ;

    # Identifiant métier
    person:birthName      "Marie Martin" ;

    # Adresse (lien vers Core Location — voir tutoriel 03)
    person:placeOfBirth   <https://data.example.gouv.fr/commune/75056> .
```

> **Bonne pratique** : utilisez des URI pérennes sous votre domaine officiel (ex. `data.monministere.gouv.fr`).

---

## 3. Valider votre fichier

### Option A — En ligne (sans installation)
1. Ouvrez [https://www.w3.org/RDF/Validator/](https://www.w3.org/RDF/Validator/)
2. Collez votre Turtle dans le champ texte
3. Cliquez sur **Parse RDF**

### Option B — En local avec `rapper`
```bash
# Installation (Debian/Ubuntu)
sudo apt install raptor2-utils

# Validation
rapper --input turtle --output ntriples mon-fichier.ttl
```

---

## 4. Vérifiez votre compréhension

- [ ] Pouvez-vous identifier le **sujet**, le **prédicat** et l'**objet** dans l'exemple ci-dessus ?
- [ ] Quelle est la différence entre `schema:familyName` et `person:birthName` ?
- [ ] Pourquoi utilise-t-on `^^xsd:date` pour la date de naissance ?

---

## Étape suivante

→ [Tutoriel 02 — Décrire une organisation avec Core Business](./02-core-business.md)

---

## Ressources

- [Spécification Core Person Vocabulary (W3C)](https://www.w3.org/TR/vocab-person/)
- [SEMIC Core Vocabularies](https://joinup.ec.europa.eu/collection/semantic-interoperability-community-semic/solution/core-person-vocabulary)
