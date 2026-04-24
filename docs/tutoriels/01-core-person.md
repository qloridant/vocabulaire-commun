# Tutoriel 01 — Mon premier triplet RDF avec Core Person

> **Objectif** : Décrire une personne en RDF/Turtle en utilisant le Core Person Vocabulary de l'UE.  
> **Durée estimée** : 30 minutes  
> **Niveau** : Débutant

---

## Ce que vous allez apprendre

- Ce qu'est un triplet RDF
- Pourquoi et comment le **Core Person Vocabulary** intervient
- Quelles propriétés utiliser et lesquelles ajouter selon votre besoin
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

Trois éléments à repérer :

- **Sujet** : `<https://data.example.gouv.fr/personne/marie-dupont>` — l'URI unique qui identifie cette personne
- **Prédicat** : `schema:givenName` — la propriété qui décrit le sujet
- **Objet** : `"Marie"` — la valeur (ici un texte littéral)

---

## 2. Pourquoi le Core Person Vocabulary intervient-il ?

Sans vocabulaire commun, chaque système invente ses propres noms de champs :

| Système A | Système B | Système C |
|---|---|---|
| `prenom` | `first_name` | `givenName` |
| `nom_naissance` | `birth_name` | `maiden_name` |
| `date_naissance` | `dob` | `birthDate` |

Ces champs sont **sémantiquement équivalents** mais **techniquement incompatibles**. Aucun outil ne peut les relier automatiquement.

Le **Core Person Vocabulary** (CPV) résout ce problème en définissant des propriétés dont l'URI fait office de référence universelle :

| Besoin | Propriété CPV | URI complète |
|---|---|---|
| Nom de naissance | `person:birthName` | `http://www.w3.org/ns/person#birthName` |
| Lieu de naissance | `person:placeOfBirth` | `http://www.w3.org/ns/person#placeOfBirth` |
| Nationalité | `person:citizenship` | `http://www.w3.org/ns/person#citizenship` |
| Genre | `person:gender` | `http://www.w3.org/ns/person#gender` |

Deux systèmes qui utilisent `person:birthName` parlent **exactement** de la même chose, quelle que soit leur technologie sous-jacente.

!!! tip "CPV + schema.org"
    Le Core Person Vocabulary ne redéfinit pas ce qui existe déjà ailleurs. Il délègue à **schema.org** les propriétés génériques (`givenName`, `familyName`, `birthDate`) et ne définit que ce qui est spécifique au contexte administratif (`birthName`, `placeOfBirth`, `citizenship`…).

---

## 3. Construire une description pas à pas

### Étape A — Le minimum : déclarer une personne

```turtle
@prefix person: <http://www.w3.org/ns/person#> .

<https://data.example.gouv.fr/personne/marie-dupont>
    a person:Person .
```

`a` est un raccourci Turtle pour `rdf:type`. Cette ligne déclare que la ressource est une instance de `person:Person`.

### Étape B — Ajouter l'identité civile

```turtle
@prefix person: <http://www.w3.org/ns/person#> .
@prefix schema: <http://schema.org/> .
@prefix xsd:    <http://www.w3.org/2001/XMLSchema#> .

<https://data.example.gouv.fr/personne/marie-dupont>
    a person:Person ;

    # Nom d'usage (schema.org)
    schema:givenName      "Marie" ;
    schema:familyName     "Dupont" ;

    # Nom de naissance — propriété spécifique CPV
    # Utile si différent du nom d'usage (mariage, changement légal…)
    person:birthName      "Marie Martin" ;

    # Date de naissance typée xsd:date (et non texte libre)
    schema:birthDate      "1985-04-12"^^xsd:date .
```

!!! warning "Toujours typer les dates"
    `"1985-04-12"` sans type est une chaîne de caractères — les moteurs SPARQL ne peuvent pas la comparer ni la trier. Avec `^^xsd:date`, c'est une date exploitable : `FILTER(?naissance > "1980-01-01"^^xsd:date)`.

### Étape C — Ajouter la localisation et la nationalité

```turtle
@prefix person: <http://www.w3.org/ns/person#> .
@prefix schema: <http://schema.org/> .
@prefix xsd:    <http://www.w3.org/2001/XMLSchema#> .

<https://data.example.gouv.fr/personne/marie-dupont>
    a person:Person ;
    schema:givenName      "Marie" ;
    schema:familyName     "Dupont" ;
    person:birthName      "Marie Martin" ;
    schema:birthDate      "1985-04-12"^^xsd:date ;

    # Lieu de naissance — URI vers une commune (Core Location)
    person:placeOfBirth   <https://data.example.gouv.fr/commune/75056> ;

    # Pays de naissance — URI vers le référentiel Publications Office EU
    person:countryOfBirth
        <http://publications.europa.eu/resource/authority/country/FRA> ;

    # Nationalité — même référentiel
    person:citizenship
        <http://publications.europa.eu/resource/authority/country/FRA> .
```

Les valeurs de `person:countryOfBirth` et `person:citizenship` sont des **URI vers des référentiels contrôlés**, pas des chaînes de caractères comme `"France"`. Cela permet de les relier à d'autres jeux de données qui utilisent les mêmes URI.

---

## 4. Quelles propriétés peut-on encore ajouter ?

Le Core Person Vocabulary définit d'autres propriétés selon votre contexte :

| Propriété | Utilité | Exemple |
|---|---|---|
| `person:patronymicName` | Nom patronymique (droit civil) | `"Dupont"` |
| `person:matronymicName` | Nom matronymique | `"Martin"` |
| `person:placeOfDeath` | Lieu de décès | URI commune |
| `person:countryOfDeath` | Pays de décès | URI pays EU |
| `person:residency` | Lieu de résidence actuel | URI commune ou adresse |
| `person:gender` | Genre (valeur contrôlée) | URI référentiel EU |

Pour connaître l'ensemble des propriétés disponibles, leurs définitions et leurs contraintes, consultez la **référence complète** :

→ **[Référence — Core Person Vocabulary](../references/core-person.md)**  
→ **[Spécification W3C officielle](https://www.w3.org/TR/vocab-person/)**  
→ **[Fiche SEMIC avec exemples](https://joinup.ec.europa.eu/collection/semantic-interoperability-community-semic/solution/core-person-vocabulary)**

---

## 5. Valider votre fichier

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

Une sortie sans erreur confirme que votre Turtle est syntaxiquement valide.

---

## 6. Vérifiez votre compréhension

- [ ] Pourquoi utilise-t-on une URI pour `person:citizenship` plutôt que la chaîne `"française"` ?
- [ ] Quelle est la différence entre `schema:familyName` et `person:birthName` ? Dans quel cas les deux valeurs diffèrent-elles ?
- [ ] Pourquoi `^^xsd:date` est-il important pour les requêtes SPARQL ?
- [ ] Si vous devez décrire le lieu de résidence actuel d'une personne, quelle propriété CPV utilisez-vous ?

---

## Étape suivante

→ [Tutoriel 02 — Décrire une organisation avec Core Business](./02-core-business.md)

---

## Références

- [Référence interne — Core Person Vocabulary](../references/core-person.md) — toutes les propriétés avec exemples
- [Spécification W3C — Core Person Vocabulary](https://www.w3.org/TR/vocab-person/)
- [SEMIC — Core Person Vocabulary](https://joinup.ec.europa.eu/collection/semantic-interoperability-community-semic/solution/core-person-vocabulary)
- [Publications Office EU — Référentiel pays](https://op.europa.eu/en/web/eu-vocabularies/dataset/-/resource?uri=http://publications.europa.eu/resource/dataset/country)
- [Publications Office EU — Référentiel genres](https://op.europa.eu/en/web/eu-vocabularies/dataset/-/resource?uri=http://publications.europa.eu/resource/dataset/human-sex)