# Guide — Publier ses données en JSON-LD

> **Problème** : Mon API REST renvoie du JSON. Comment y intégrer de la sémantique sans tout réécrire ?  
> **Solution** : Ajouter un contexte JSON-LD.

---

## Quand utiliser ce guide

- Vous avez déjà une API JSON existante
- Vous souhaitez la rendre compatible Web sémantique
- Vous ne voulez pas changer le format de réponse principal

---

## 1. Ajouter un contexte JSON-LD à une réponse existante

Réponse JSON actuelle :

```json
{
  "id": "org-42",
  "nom": "Direction du Numérique",
  "siret": "13001518700013",
  "ville": "Paris"
}
```

Réponse enrichie en JSON-LD :

```json
{
  "@context": {
    "@vocab": "http://www.w3.org/ns/legal#",
    "nom": "legalName",
    "siret": "http://schema.org/identifier",
    "ville": "http://www.w3.org/ns/locn#postName",
    "id": "@id",
    "@base": "https://data.example.gouv.fr/org/"
  },
  "@type": "LegalEntity",
  "id": "org-42",
  "nom": "Direction du Numérique",
  "siret": "13001518700013",
  "ville": "Paris"
}
```

> **Avantage** : les données existantes sont **inchangées**. Seul le contexte est ajouté.

---

## 2. Exposer le contexte via Content-Type

Configurez votre serveur pour retourner le bon en-tête :

```http
Content-Type: application/ld+json
```

Exemple avec **nginx** :

```nginx
location /api/ {
    add_header Content-Type "application/ld+json; charset=utf-8";
}
```

Exemple avec **Python / FastAPI** :

```python
from fastapi import FastAPI
from fastapi.responses import JSONResponse

app = FastAPI()

@app.get("/org/{org_id}")
def get_organisation(org_id: str):
    data = {
        "@context": {
            "@vocab": "http://www.w3.org/ns/legal#",
            "@base": "https://data.example.gouv.fr/org/",
            "nom": "legalName"
        },
        "@type": "LegalEntity",
        "@id": org_id,
        "nom": "Direction du Numérique"
    }
    return JSONResponse(content=data, media_type="application/ld+json")
```

---

## 3. Externaliser le contexte (recommandé)

Pour éviter de répéter le contexte dans chaque réponse, publiez-le à une URL stable :

```json
{
  "@context": "https://data.example.gouv.fr/contexts/organisation.jsonld",
  "@type": "LegalEntity",
  "@id": "https://data.example.gouv.fr/org/org-42",
  "legalName": "Direction du Numérique"
}
```

Fichier `organisation.jsonld` publié sur votre serveur :

```json
{
  "@context": {
    "legal": "http://www.w3.org/ns/legal#",
    "schema": "http://schema.org/",
    "locn": "http://www.w3.org/ns/locn#",
    "LegalEntity": "legal:LegalEntity",
    "legalName": "legal:legalName",
    "identifier": "schema:identifier",
    "address": "locn:address"
  }
}
```

> **Important** : cette URL doit être **pérenne**. Utilisez votre domaine officiel `*.gouv.fr`.

---

## 4. Valider votre JSON-LD

Utilisez le **JSON-LD Playground** :  
→ [https://json-ld.org/playground/](https://json-ld.org/playground/)

Collez votre JSON-LD et vérifiez la conversion en N-Quads ou Turtle.

---

## Pièges courants

| Problème | Solution |
|---|---|
| Contexte non résolvable | Hébergez le fichier `.jsonld` sur un serveur public |
| `@id` relatif non résolu | Déclarez `@base` dans le contexte |
| Propriétés non mappées | Toute propriété sans contexte est ignorée par les parseurs RDF |
| CORS bloqué | Activez les en-têtes `Access-Control-Allow-Origin` |

---

## Étape suivante

→ [Guide — Interroger un triplestore avec SPARQL](./02-sparql-requetes.md)
