# Référence — Core Public Service Vocabulary Application Profile (CPSV-AP)

**Namespace CPSV** : `http://purl.org/vocab/cpsv#`  
**Namespace CV** : `http://data.europa.eu/m8g/`  
**Préfixes** : `cpsv:` et `cv:`  
**Spécification** : [https://semiceu.github.io/CPSV-AP/](https://semiceu.github.io/CPSV-AP/)

---

## Classes principales

| Classe | Namespace | Description |
|---|---|---|
| `cpsv:PublicService` | `cpsv:` | Service public fourni par une administration |
| `cv:Evidence` | `cv:` | Pièce justificative requise |
| `cpsv:Output` | `cpsv:` | Résultat produit par le service |
| `cv:Channel` | `cv:` | Canal d'accès (web, guichet, courrier…) |
| `cv:Cost` | `cv:` | Coût associé |
| `cv:LegalResource` | `cv:` | Texte juridique encadrant le service |
| `cv:PublicOrganisation` | `cv:` | Organisme public compétent |

---

## Propriétés de `cpsv:PublicService`

| Propriété | Type | Description |
|---|---|---|
| `dct:title` | `rdfs:Literal` | Intitulé du service |
| `dct:description` | `rdfs:Literal` | Description |
| `dct:language` | `dct:LinguisticSystem` | Langue(s) du service |
| `dct:type` | `skos:Concept` | Type de service |
| `cv:hasCompetentAuthority` | `cv:PublicOrganisation` | Organisme responsable |
| `cpsv:hasChannel` | `cv:Channel` | Canal d'accès |
| `cpsv:hasInput` | `cv:Evidence` | Pièce(s) requise(s) |
| `cpsv:produces` | `cpsv:Output` | Résultat(s) produit(s) |
| `cv:hasCost` | `cv:Cost` | Coût |
| `cv:hasLegalResource` | `cv:LegalResource` | Base légale |
| `cv:processingTime` | `xsd:duration` | Délai de traitement |

---

## Exemple minimal

```turtle
@prefix cpsv: <http://purl.org/vocab/cpsv#> .
@prefix cv:   <http://data.europa.eu/m8g/> .
@prefix dct:  <http://purl.org/dc/terms/> .

<https://data.example.gouv.fr/service/extrait-acte-naissance>
    a cpsv:PublicService ;
    dct:title                  "Demande d'extrait d'acte de naissance"@fr ;
    dct:description            "Obtenir une copie d'acte de naissance auprès de la mairie du lieu de naissance."@fr ;
    cv:hasCompetentAuthority   <https://data.example.gouv.fr/org/mairie-lyon> ;
    cpsv:hasChannel            <https://data.example.gouv.fr/canal/service-public-fr> ;
    cpsv:produces              <https://data.example.gouv.fr/output/extrait-acte-naissance> ;
    cv:processingTime          "P10D"^^xsd:duration .
```

---

## Types de canaux (`cv:Channel`)

| Valeur | Description |
|---|---|
| `cv:Online` | Démarche en ligne |
| `cv:InPerson` | Accueil physique |
| `cv:ByPost` | Courrier postal |
| `cv:ByPhone` | Téléphone |
| `cv:ByEmail` | Courriel |

---

## Coût

```turtle
@prefix cv:  <http://data.europa.eu/m8g/> .
@prefix dct: <http://purl.org/dc/terms/> .

<https://data.example.gouv.fr/cout/extrait-gratuit>
    a cv:Cost ;
    cv:value         "0"^^xsd:decimal ;
    cv:currency      <http://publications.europa.eu/resource/authority/currency/EUR> ;
    dct:description  "Gratuit pour les personnes concernées."@fr .
```

---

## Voir aussi

- [Tutoriel 04 — Décrire un service public avec CPSV](../tutoriels/04-core-public-service.md)
- [CPSV-AP sur Joinup](https://joinup.ec.europa.eu/collection/semantic-interoperability-community-semic/solution/core-public-service-vocabulary-application-profile)
- [service-public.fr](https://www.service-public.fr)
