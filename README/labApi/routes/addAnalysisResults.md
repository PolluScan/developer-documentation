###  Soumettre les résultats d’analyse des échantillons

**`POST /api/labs/orders/{orderId}/samples/results`**

Permet de soumettre les résultats d’analyse de tous les échantillons en une seule fois.

**Paramètres d’URL**

- `{orderId}` : Identifiant de la commande.

#### Corps de la requête

```typescript
{
  results: {
    sample_id: string; // Identifiant d'échantillon unique (dans des échantillons Array = Sample.ID)
    detected: boolean; // Si un polluant est détecté === TRUE, si un polluant n'est pas détecté === FALSE
    internal_note: string; // Commentaire relatif au resultat de l'analyse
    reported_value: string;
    unit: string;
    margin_of_error: float
    meta_data: any; // un objet JSON sans type précis, contenant les données brutes des résultats du laboratoire
  }
  [];
}
```


### Index

- [Mettre à jour le statut des commandes](./routes/updateOrderStatus.md)
- [Téléverser les rapports PDF finaux](./routes/uploadAnalysisReport.md)
- [Consulter la liste des commandes en attente](./routes/fetchOpenOrders.md)