---
title: Mettre à jour le statut d’une commande
parent: Routes
nav_order: 1
---

## Mettre à jour le statut d’une commande

**`PUT /api/labs/orders/{orderId}/status`**

Utilisez cet endpoint pour mettre à jour le statut d’une commande.

**Paramètres d’URL**

- `{orderId}` : Identifiant unique de la commande.

### Corps de la requête

```typescript
{
  status: string; // Nouveau statut de la commande
}
```

### Statuts disponibles

- `lost` : Commande perdue ou détruite
- `received` : Commande reçue
- `analysis_wip` : Analyse en cours
- `analysis_done` : Analyse terminée
- `results_available` : Résultats disponibles pour le diagnostiqueur

### Passage à `results_available`

Ce statut indique que tous les résultats de la commande ont été livrés via [Soumettre les résultats d’analyse]({{ '/docs/laboratories/api/docs/routes/add-analysis-results.html' | relative_url }}).

- **Condition** : chaque échantillon non perdu de la commande doit avoir un résultat de laboratoire. Les échantillons déclarés dans `notAnalysed` sont perdus et ne sont pas comptés.
- **Effets** : la date et l’auteur du passage sont enregistrés, les échantillons passent au statut `analyzed`, et le diagnostiqueur est notifié (e-mail et notification) pour valider les résultats.
- **Verrouillage** : à partir de ce statut, l’envoi de résultats pour la commande est refusé (`409 ERROR_PARCEL_RESULTS_LOCKED`).

Si un échantillon non perdu n’a pas de résultat :

```json
{ "statusCode": 409, "message": "ERROR_PARCEL_RESULTS_INCOMPLETE" }
```
