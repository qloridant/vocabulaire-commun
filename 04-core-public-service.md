# Tutoriel 04 — Décrire un service public avec CPSV-AP

> **Objectif** : Modéliser un service administratif avec le Core Public Service Vocabulary Application Profile (CPSV-AP).  
> **Durée estimée** : 40 minutes  
> **Niveau** : Intermédiaire

---

## Ce que vous allez apprendre

- La structure de **CPSV-AP**
- Comment décrire un service, ses entrées et ses sorties
- Comment lier un service à un organisme et une règle juridique

---

## 1. Présentation de CPSV-AP

Le **CPSV-AP** est un profil d'application basé sur le Core Public Service Vocabulary. Il est recommandé par la Commission européenne pour décrire les services publics de manière interopérable.

Namespaces principaux :

```turtle
@prefix cpsv:  <http://purl.org/vocab/cpsv#> .
@prefix cv:    <http://data.europa.eu/m8g/> .
@prefix dct:   <http://purl.org/dc/terms/> .
@prefix dcat:  <http://www.w3.org/ns/dcat#> .
```

---

## 2. Exemple — Demande de carte grise

```turtle
@prefix cpsv:  <http://purl.org/vocab/cpsv#> .
@prefix cv:    <http://data.europa.eu/m8g/> .
@prefix dct:   <http://purl.org/dc/terms/> .
@prefix schema: <http://schema.org/> .
@prefix locn:  <http://www.w3.org/ns/locn#> .

# --- Le service ---
<https://data.example.gouv.fr/service/immatriculation-vehicule>
    a cpsv:PublicService ;

    dct:title           "Immatriculation d'un véhicule"@fr ;
    dct:description     "Obtenir un certificat d'immatriculation (carte grise) pour un véhicule."@fr ;
    dct:language        <http://publications.europa.eu/resource/authority/language/FRA> ;

    # Organisme responsable
    cv:hasCompetentAuthority <https://data.example.gouv.fr/org/ants> ;

    # Canal(aux) d'accès
    cpsv:hasChannel     <https://data.example.gouv.fr/canal/immat-web> ;

    # Pièces requises (entrées)
    cpsv:hasInput       <https://data.example.gouv.fr/evidence/certificat-cession> ,
                        <https://data.example.gouv.fr/evidence/justificatif-domicile> ;

    # Résultat produit (sortie)
    cpsv:produces       <https://data.example.gouv.fr/output/certificat-immatriculation> ;

    # Base légale
    cv:hasLegalResource <https://data.example.gouv.fr/legal/code-route-R322-1> .
```

---

## 3. Décrire les entrées (pièces justificatives)

```turtle
@prefix cv:  <http://data.europa.eu/m8g/> .
@prefix dct: <http://purl.org/dc/terms/> .

<https://data.example.gouv.fr/evidence/certificat-cession>
    a cv:Evidence ;
    dct:title       "Certificat de cession"@fr ;
    dct:description "Formulaire Cerfa n°15776 signé par le vendeur et l'acheteur."@fr ;
    cv:isConformantTo <https://www.service-public.fr/particuliers/vosdroits/R13567> .
```

---

## 4. Décrire la sortie (résultat du service)

```turtle
@prefix cpsv: <http://purl.org/vocab/cpsv#> .
@prefix dct:  <http://purl.org/dc/terms/> .

<https://data.example.gouv.fr/output/certificat-immatriculation>
    a cpsv:Output ;
    dct:title       "Certificat d'immatriculation"@fr ;
    dct:type        <http://publications.europa.eu/resource/authority/output-type/CERTIFICATE> .
```

---

## 5. Décrire le canal d'accès

```turtle
@prefix cv:     <http://data.europa.eu/m8g/> .
@prefix schema: <http://schema.org/> .
@prefix dct:    <http://purl.org/dc/terms/> .

<https://data.example.gouv.fr/canal/immat-web>
    a cv:Channel ;
    dct:type        <http://data.europa.eu/m8g/Online> ;
    schema:url      <https://immatriculation.ants.gouv.fr> ;
    dct:title       "Démarche en ligne — ANTS"@fr .
```

---

## Vérifiez votre compréhension

- [ ] Quelle propriété lie un service à l'organisme qui en est responsable ?
- [ ] Comment distinguer une entrée (preuve) d'une sortie dans CPSV ?
- [ ] Où placer la base légale d'un service ?

---

## Étape suivante

→ [Guide — Publier ses données en JSON-LD](../guides/01-jsonld-publication.md)
