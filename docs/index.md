# Web Sémantique — Administrations françaises

!!! tip "Vocabulaires prioritaires"
    Cette documentation met en avant les **Core Vocabularies** de l'Union européenne, maintenus par la Commission européenne via le programme [SEMIC](https://joinup.ec.europa.eu/collection/semantic-interoperability-community-semic).

---

## Pourquoi cette documentation ?

Les administrations françaises publient des données hétérogènes, fragmentées, souvent incomparables entre services. Le Web sémantique, via des vocabulaires standardisés, permet :

- l'**interopérabilité** entre systèmes (DINUM, collectivités, Europe)
- la **réutilisation** des données publiques (open data)
- l'**alignement avec les exigences européennes** (INSPIRE, ISA², SEMIC, Data Governance Act)

---

## Structure de la documentation

Cette documentation suit le cadre **[Diátaxis](https://diataxis.fr)** :

| Section | Objectif | Pour qui |
|---|---|---|
| [Explications](./explication/01-web-semantique.md) | Comprendre les concepts | Tous |
| [Tutoriels](./tutoriels/01-core-person.md) | Apprendre par la pratique | Débutants |
| [Guides pratiques](./guides/01-jsonld-publication.md) | Résoudre un problème concret | Praticiens |
| [Références](./references/core-person.md) | Consulter une spécification | Développeurs |

---

## Vocabulaires Core de l'UE — Vue d'ensemble

| Vocabulaire | Domaine | Namespace | Préfixe |
|---|---|---|---|
| [Core Person](./references/core-person.md) | Personnes physiques | `http://www.w3.org/ns/person#` | `person:` |
| [Core Business](./references/core-business.md) | Organisations, personnes morales | `http://www.w3.org/ns/legal#` | `legal:` |
| [Core Location](./references/core-location.md) | Adresses, géométries | `http://www.w3.org/ns/locn#` | `locn:` |
| [CPSV-AP](./references/core-public-service.md) | Services publics, démarches | `http://purl.org/vocab/cpsv#` | `cpsv:` |
| CCCEV | Critères, pièces justificatives | `http://data.europa.eu/m8g/` | `cv:` |

---

## Parcours recommandé

```
Explication (concepts)
    → Tutoriel 01 · Core Person
    → Tutoriel 02 · Core Business
    → Tutoriel 03 · Core Location
    → Tutoriel 04 · CPSV-AP
        → Guide · Publier en JSON-LD
        → Guide · Requêtes SPARQL
        → Guide · Alignement référentiels EU
```

---

## Prérequis

- Notions de base en XML/JSON
- Connaissance des API REST
- Optionnel : bases de RDF/Turtle

---

## Liens utiles

- [SEMIC — Semantic Interoperability Community](https://joinup.ec.europa.eu/collection/semantic-interoperability-community-semic)
- [EU Vocabularies — Publications Office](https://op.europa.eu/en/web/eu-vocabularies)
- [data.europa.eu — portail open data EU](https://data.europa.eu)
- [schema.gouv.fr — schémas français](https://schema.gouv.fr)
- [API Base Adresse Nationale](https://api-adresse.data.gouv.fr)
- [JSON-LD Playground](https://json-ld.org/playground/)
