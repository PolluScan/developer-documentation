---
title: Schéma de résultat
parent: API Laboratoires
nav_order: 4
---

## Schéma de résultat d’un service

Chaque service d’analyse déclare la forme du résultat qu’il renvoie pour **un** échantillon, sous la forme d’un objet [JSON Schema 2020-12](https://json-schema.org/draft/2020-12). PolluScan s’en sert pour :

- valider chaque résultat reçu sur [Soumettre les résultats d’analyse]({{ '/docs/laboratories/api/docs/routes/add-analysis-results.html' | relative_url }}) ;
- afficher les valeurs mesurées au diagnostiqueur, qui pose ensuite le verdict (positif / négatif) échantillon par échantillon.

Le contrat est défini par PolluScan : le laboratoire adapte sa sortie au schéma de chaque service. Le schéma se déclare dans le formulaire du service (voir [Configuration]({{ '/docs/laboratories/api/docs/configuration.html' | relative_url }})). Un service sans schéma ne peut pas recevoir de résultats.

### Sous-ensemble supporté

Le schéma est vérifié à l’enregistrement du service. Sont acceptés :

- une racine `type: "object"` avec `properties` ;
- des objets imbriqués ;
- des tableaux d’objets ou de valeurs scalaires ;
- les unions de `type` avec `"null"` (ex : `["number", "null"]`) ;
- `enum`, `required`, `additionalProperties`, `description` ;
- les autres mots-clés de validation standard, par exemple `minimum` ou `minLength`.

Ne sont **pas** acceptés : `$ref`, `$defs`, `allOf`, `anyOf`, `oneOf`, `if` / `then` / `else`, `not`, `patternProperties`, `dependentSchemas`, `unevaluatedProperties`, `format`. Gardez une structure assez plate pour être parcourue.

La racine doit porter `examples: [payload]`, et `examples[0]` doit être valide contre le schéma.

Un schéma refusé renvoie la liste de toutes les violations :

```json
{
  "statusCode": 400,
  "message": "ERROR_INVALID_RESULT_SCHEMA",
  "violations": ["..."]
}
```

### Exemple

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "examples": [{ "pb": 2340, "pbLoq": 5, "cd": null, "cdLoq": 0.5, "ag": null, "agLoq": null }],
  "properties": {
    "pb": {
      "type": ["number", "null"],
      "x-label": { "fr": "Plomb", "en": "Lead", "de": "Blei", "it": "Piombo" },
      "x-unit": "mg/kg",
      "x-headline": true,
      "description": "Lead, total digestion"
    },
    "pbLoq": { "type": ["number", "null"], "x-unit": "mg/kg" },
    "cd": { "type": ["number", "null"], "x-label": "Cadmium", "x-unit": "mg/kg" },
    "concentration": {
      "type": ["number", "null"],
      "x-label": "Concentration",
      "x-unit": "fibres/cm²"
    },
    "detectionLimit": { "type": "number", "x-loq-of": "concentration" }
  }
}
```

### Mots-clés d’affichage

Tous optionnels ; en leur absence, la valeur de repli s’applique.

| Mot-clé       | Sur       | Signification                                                                 | Repli                                           |
| ------------- | --------- | ----------------------------------------------------------------------------- | ----------------------------------------------- |
| `x-label`     | propriété | Libellé : objet `{fr, en, de, it}` ou simple chaîne                           | `title`, puis la clé rendue lisible             |
| `x-unit`      | propriété | Unité affichée après la valeur                                                | aucune                                          |
| `x-headline`  | propriété | Valeur affichée sur la ligne repliée de l’échantillon (3 au maximum)          | les 3 premières valeurs mesurées non nulles     |
| `x-loq-of`    | propriété | Cette propriété est la limite de quantification de `<clé>` (si elle ne s’appelle pas `<clé>Loq`) | convention de nommage `<clé>Loq` |
| `x-hidden`    | propriété | Jamais affichée (identifiants, `@context`)                                    | les clés préfixées par `@` sont masquées d’office |
| `description` | propriété | Texte d’aide sous le libellé dans le tableau détaillé                         | aucun                                           |
| `x-analyte`   | propriété | **Réservé**, ignoré pour l’instant                                            |                                                 |
| `examples`    | racine    | `[payload]` : aperçu et laboratoire de test                                   | **obligatoire**                                 |

`x-analyte` est réservé à un futur rattachement des propriétés à des analytes canoniques (seuils, estimation automatique). Ne l’utilisez pas pour autre chose.

### Conventions

Détectées sans mot-clé :

- **Limite de quantification** : une propriété `<clé>Loq` est associée à `<clé>` et n’est pas affichée comme une ligne à part.
- **Valeur inférieure à la LOQ** : envoyez `null` comme valeur avec une LOQ non nulle ; l’affichage est `< {loq} {unité}`.
- **Non commandé** : valeur et LOQ toutes deux `null` ; la paire n’est pas affichée.
- Un objet imbriqué s’affiche comme un sous-tableau, un tableau d’objets comme des lignes.
- Les booléens s’affichent oui / non ; les `enum` et chaînes tels quels.
- Les clés du résultat absentes du schéma sont ajoutées en fin de tableau avec la clé rendue lisible (jamais ignorées silencieusement).

Exemple, pour les métaux lourds : `"pb": 245.3, "pbLoq": 0.5` s’affiche `245,3 mg/kg` ; `"cd": null, "cdLoq": 0.1` s’affiche `< 0,1 mg/kg` ; `"hg": null, "hgLoq": null` n’est pas affiché.

### Verdict du laboratoire

Le laboratoire livre des valeurs, pas de verdict. Seul le booléen `asbestosDetected` (amiante dans les matériaux) est repris comme estimation du laboratoire ; pour toutes les autres analyses, le diagnostiqueur lit le tableau et décide.

### Exemple obligatoire et aperçu

`examples[0]` est obligatoire et doit être valide contre le schéma. Dans le formulaire du service, il est affiché en direct pendant l’édition, avec le même rendu que celui du diagnostiqueur, ainsi que le nombre de propriétés sans libellé ou sans unité. Il sert aussi de résultat au laboratoire de test.

### Versionnement du schéma
{: #versionnement}

- La **première livraison** de résultats pour une commande est validée contre le schéma courant du service, qui est alors copié sur la commande.
- Les **livraisons suivantes** pour cette commande sont validées contre cette copie, même si le schéma du service a été modifié entre-temps. Les nouvelles commandes utilisent le schéma courant.
- `resultSchemaHash` identifie la version attendue : empreinte SHA-256 (hexadécimal) du JSON canonique du schéma copié sur la commande, à défaut du schéma courant du service, `null` si le service n’a pas de schéma. JSON canonique : clés d’objet triées récursivement, sans espaces. Il est renvoyé pour chaque commande par [Lister toutes les commandes ouvertes]({{ '/docs/laboratories/api/docs/routes/fetch-open-orders.html' | relative_url }}) et dans chaque réponse 422.

Traitez `resultSchemaHash` comme un identifiant opaque : comparez-le à l’empreinte du schéma que votre système produit (même recette) pour savoir contre quel schéma une commande ouverte sera validée.

### Schémas de référence

PolluScan fournit un schéma de référence annoté pour chaque analyse du laboratoire intégré : `asbestos-in-material`, `asbestos-in-dust`, `asbestos-in-air`, `pcb`, `pcb-cp`, `pah`, `heavy-metals`. L’identifiant correspond au `lab_service.slug` de la commande. Un exemple de résultat par analyse figure sur [Soumettre les résultats d’analyse]({{ '/docs/laboratories/api/docs/routes/add-analysis-results.html' | relative_url }}#exemples-par-analyse). Pour un nouveau service, le laboratoire rédige son propre schéma.
