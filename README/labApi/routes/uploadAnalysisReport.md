###  Téléverser le rapport final

**`POST /api/labs/orders/{orderId}/report`**

Permet de téléverser le rapport PDF final d’une commande, une fois toutes les analyses terminées.

**Paramètres d’URL**

- `{orderId}` : Identifiant de la commande.

#### Corps de la requête

Payload **multipart/form-data**

- `file` : Fichier PDF du rapport final

### Index 

- [Mettre à jour le statut des commandes](./routes/updateOrderStatus.md)
- [Soumettre les résultats d'analyse des échantillons](./routes/addAnalysisResults.md)
- [Consulter la liste des commandes en attente](./routes/fetchOpenOrders.md)