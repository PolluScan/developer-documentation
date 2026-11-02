---
title: Soumettre les résultats d’analyse des échantillons
parent: Routes
nav_order: 2
---

## Soumettre les résultats d’analyse des échantillons

**`POST /api/labs/orders/{orderId}/samples/results`**

Permet de soumettre les résultats d’analyse des échantillons d’une commande. Chaque résultat est validé contre le [schéma de résultat]({{ '/docs/laboratories/api/docs/result-schema.html' | relative_url }}) du service commandé avant d’être enregistré.

**Paramètres d’URL**

- `{orderId}` : Identifiant de la commande. La commande doit appartenir au laboratoire identifié par l’en-tête `X-Lab-Id`.

### Corps de la requête

```typescript
{
  results: {
    sampleId: string; // Identifiant de l'échantillon (samples_data[].id de la commande)
    result: object; // Résultat d'UN échantillon, conforme au schéma de résultat du service
  }[];
  notAnalysed?: {
    sampleId: string; // Identifiant de l'échantillon qui n'a pas pu être analysé
    reason: string; // Raison, affichée telle quelle au diagnostiqueur (ex: "insufficient material")
  }[];
}
```

- `results` : un élément par échantillon analysé. L’objet `result` est validé contre le schéma de résultat du service (voir [Schéma de résultat]({{ '/docs/laboratories/api/docs/result-schema.html' | relative_url }})).
- `notAnalysed` : échantillons que le laboratoire n’a pas pu analyser (matière insuffisante, échantillon que le service ne peut pas traiter, etc.). Ces échantillons passent au statut perdu et sont affichés « Non analysé · raison » au diagnostiqueur.
- Chaque `sampleId` doit appartenir à la commande `{orderId}`.
- Les résultats peuvent être livrés en plusieurs envois tant que la commande n’est pas au statut `results_available`. Chaque échantillon ne doit être livré qu’une seule fois.
- La validation porte sur l’envoi entier : **si un seul échantillon est en erreur, rien n’est enregistré**. Corrigez et renvoyez l’envoi complet.

Le laboratoire ne pose pas de verdict positif / négatif : il livre des valeurs, le diagnostiqueur valide chaque échantillon. Seul le champ `asbestosDetected` (amiante dans les matériaux) est repris comme estimation du laboratoire.

### Réponses

#### 200 : résultats enregistrés

```typescript
{
  results: {
    id: number; // Identifiant du résultat créé
    sample: string; // Identifiant de l'échantillon
    status: "complete";
    detected: boolean | null; // Estimation du laboratoire : asbestosDetected si présent, sinon null
    rawResult: object; // L'objet `result` envoyé, ou { notAnalysed: reason }
  }[];
}
```

Les échantillons livrés passent au statut `analyzed`, ceux de `notAnalysed` au statut `lost`.

#### 404 : commande introuvable

La commande n’existe pas ou n’appartient pas au laboratoire `X-Lab-Id`.

```json
{ "statusCode": 404, "message": "ERROR_PARCEL_NOT_FOUND" }
```

#### 409 : service sans schéma de résultat

Le service commandé n’a pas de schéma de résultat. Déclarez-le dans le formulaire du service (voir [Configuration]({{ '/docs/laboratories/api/docs/configuration.html' | relative_url }})) puis renvoyez les résultats.

```json
{ "statusCode": 409, "message": "ERROR_SERVICE_NO_RESULT_SCHEMA" }
```

#### 409 : résultats verrouillés

La commande est au statut `results_available` ou au-delà. Les résultats ne peuvent plus être modifiés par l’API ; pour une correction, contactez le diagnostiqueur.

```json
{ "statusCode": 409, "message": "ERROR_PARCEL_RESULTS_LOCKED" }
```

#### 422 : résultats non conformes

Un ou plusieurs résultats ne respectent pas le schéma attendu. Les erreurs sont listées par échantillon ; `resultSchemaHash` identifie la version du schéma contre laquelle la commande est validée (voir [Versionnement du schéma]({{ '/docs/laboratories/api/docs/result-schema.html' | relative_url }}#versionnement)). **Rien n’est enregistré.**

```json
{
  "statusCode": 422,
  "message": "ERROR_RESULT_SCHEMA_MISMATCH",
  "resultSchemaHash": "5f0c3e9a2b7d41c8e6a09f3b5d7e1c2a4b6d8f0e1a3c5e7b9d2f4a6c8e0b1d3f",
  "errors": [
    {
      "sampleId": "3f2b8c1e-5a47-4d0b-9c6e-1b2a7d9e4f10",
      "errors": [
        { "instancePath": "/pb", "message": "must be number,null" }
      ]
    }
  ]
}
```

Le même code 422 est renvoyé avec `"message": "ERROR_SAMPLE_NOT_IN_ORDER"` lorsqu’un `sampleId` n’appartient pas à la commande ; `errors` liste alors les identifiants concernés.

### Exemple avec un échantillon non analysé

```json
{
  "results": [
    {
      "sampleId": "3f2b8c1e-5a47-4d0b-9c6e-1b2a7d9e4f10",
      "result": {
        "asbestosDetected": false,
        "contaminantType": null,
        "remarks": null
      }
    }
  ],
  "notAnalysed": [
    {
      "sampleId": "8a1d4e2c-9b3f-4c7a-a5e1-6d0f2b8c3e71",
      "reason": "insufficient material"
    }
  ]
}
```

### Exemples par analyse

Un exemple de corps de requête par analyse de référence. Chaque objet `result` est l’exemple `examples[0]` du schéma de référence du service (identifiant du service = `lab_service.slug` de la commande).

#### Amiante dans les matériaux (`asbestos-in-material`)

```json
{
  "results": [
    {
      "sampleId": "3f2b8c1e-5a47-4d0b-9c6e-1b2a7d9e4f10",
      "result": {
        "asbestosDetected": true,
        "contaminantType": "chrysotile",
        "remarks": "Sample received partially crushed."
      }
    }
  ]
}
```

#### Amiante dans les poussières (`asbestos-in-dust`)

```json
{
  "results": [
    {
      "sampleId": "8a1d4e2c-9b3f-4c7a-a5e1-6d0f2b8c3e71",
      "result": {
        "microscopeId": "af960a16-9ade-5c9e-8edb-1fa40db82184",
        "concentration": 141,
        "detectionLimit": 36,
        "observations": [
          {
            "magnification": 680,
            "fieldNumber": 3,
            "contaminantType": "chrysotile",
            "structure": "fiber",
            "weighting": 1
          },
          {
            "magnification": 680,
            "fieldNumber": 11,
            "contaminantType": "chrysotile",
            "structure": "bundle",
            "weighting": 10
          },
          {
            "magnification": 1500,
            "fieldNumber": 5,
            "contaminantType": "chrysotile",
            "structure": "matrix",
            "weighting": 5
          }
        ],
        "observedAreaCm2": 0.11,
        "weightedStructureCount": 16,
        "upperConfidenceLimit95": 229,
        "lowerConfidenceLimit95": 79
      }
    }
  ]
}
```

#### Amiante dans l’air (`asbestos-in-air`)

```json
{
  "results": [
    {
      "sampleId": "c5e7a9b1-2d4f-4e6a-8c0b-3a5d7f9e1b24",
      "result": {
        "microscopeId": "a2f5a18c-7b7d-507d-929e-9ce31eff7d31",
        "concentration": 211,
        "detectionLimit": 127,
        "respirableAsbestosFiberCount": 5,
        "actinoliteCount": null,
        "amositeCount": 1,
        "anthophylliteCount": null,
        "chrysotileCount": 4,
        "crocidoliteCount": null,
        "tremoliteCount": null,
        "organicFibersCount": 14,
        "inorganicFibersCount": 6,
        "numberOfFields": 200,
        "fieldAreaMm2": 0.037,
        "filterAreaMm2": 380,
        "weightedVolumeM3": 0.024,
        "upperConfidenceLimit95": 507,
        "lowerConfidenceLimit95": 69,
        "sampling": {
          "airVolumeM3": 1.2,
          "date": "2026-10-12",
          "temperatureCelsius": 21,
          "relativeHumidityPercent": 45
        },
        "remarks": null
      }
    }
  ]
}
```

#### PCB (`pcb`)

```json
{
  "results": [
    {
      "sampleId": "1e3c5a7b-9d2f-4b8e-a6c4-0f2d4b6a8c93",
      "result": {
        "pcb28": null,
        "pcb28Loq": 0.1,
        "pcb52": 0.85,
        "pcb52Loq": 0.1,
        "pcb101": 2.4,
        "pcb101Loq": 0.1,
        "pcb138": 3.1,
        "pcb138Loq": 0.1,
        "pcb153": 3.6,
        "pcb153Loq": 0.1,
        "pcb180": 1.9,
        "pcb180Loq": 0.1,
        "technicalMixture": "aroclor-1254",
        "sumOfCongeners": 11.85,
        "multiplicationFactor": 4.7,
        "totalPcb": 55.7,
        "remarks": null
      }
    }
  ]
}
```

#### PCB et chloroparaffines (`pcb-cp`)

```json
{
  "results": [
    {
      "sampleId": "7b9d1f3a-5c8e-4a2b-9e7d-4c6a8e0b2d15",
      "result": {
        "pcb28": null,
        "pcb28Loq": 0.1,
        "pcb52": null,
        "pcb52Loq": 0.1,
        "pcb101": null,
        "pcb101Loq": 0.1,
        "pcb138": null,
        "pcb138Loq": 0.1,
        "pcb153": null,
        "pcb153Loq": 0.1,
        "pcb180": null,
        "pcb180Loq": 0.1,
        "technicalMixture": null,
        "sumOfCongeners": null,
        "multiplicationFactor": null,
        "totalPcb": null,
        "chloroparaffinRange": {
          "@context": "https://schema.org",
          "@type": "QuantitativeValue",
          "minValue": 1,
          "maxValue": 5,
          "unitCode": "P1"
        }
      }
    }
  ]
}
```

#### HAP (`pah`)

```json
{
  "results": [
    {
      "sampleId": "4d6f8b0c-1e3a-4c5e-b7d9-2a4c6e8f0b37",
      "result": {
        "sumOf16Pah": 100.7,
        "naphthalene": null,
        "naphthaleneLoq": 0.5,
        "acenaphthylene": null,
        "acenaphthyleneLoq": 0.5,
        "acenaphthene": 0.6,
        "acenaphtheneLoq": 0.5,
        "fluorene": 1.2,
        "fluoreneLoq": 0.5,
        "phenanthrene": 14.8,
        "phenanthreneLoq": 0.5,
        "anthracene": 3.9,
        "anthraceneLoq": 0.5,
        "fluoranthene": 22.5,
        "fluorantheneLoq": 0.5,
        "pyrene": 17.3,
        "pyreneLoq": 0.5,
        "benzoAAnthracene": 8.1,
        "benzoAAnthraceneLoq": 0.5,
        "chrysene": 7.6,
        "chryseneLoq": 0.5,
        "benzoBFluoranthene": 6.9,
        "benzoBFluorantheneLoq": 0.5,
        "benzoKFluoranthene": 3.2,
        "benzoKFluorantheneLoq": 0.5,
        "benzoAPyrene": 6.4,
        "benzoAPyreneLoq": 0.5,
        "indeno123CdPyrene": 3.8,
        "indeno123CdPyreneLoq": 0.5,
        "dibenzoAhAnthracene": 0.9,
        "dibenzoAhAnthraceneLoq": 0.5,
        "benzoGhiPerylene": 3.5,
        "benzoGhiPeryleneLoq": 0.5
      }
    }
  ]
}
```

#### Métaux lourds (`heavy-metals`)

```json
{
  "results": [
    {
      "sampleId": "9f1b3d5e-7a0c-4e2d-8b6f-5d7b9a1c3e48",
      "result": {
        "ag": null,
        "agLoq": null,
        "al": null,
        "alLoq": null,
        "as": 2.1,
        "asLoq": 0.5,
        "b": null,
        "bLoq": null,
        "cd": null,
        "cdLoq": 0.1,
        "crTotal": 12.4,
        "crTotalLoq": 0.35,
        "crVi": null,
        "crViLoq": null,
        "cu": 18.7,
        "cuLoq": 0.5,
        "fe": null,
        "feLoq": null,
        "hg": null,
        "hgLoq": null,
        "mn": null,
        "mnLoq": null,
        "ni": 9.8,
        "niLoq": 0.5,
        "pb": 245.3,
        "pbLoq": 0.5,
        "sb": null,
        "sbLoq": null,
        "se": null,
        "seLoq": null,
        "zn": 310.6,
        "znLoq": 0.25,
        "toc400": 6.2,
        "toc400Loq": 1
      }
    }
  ]
}
```
