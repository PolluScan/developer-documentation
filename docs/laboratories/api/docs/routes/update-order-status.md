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
