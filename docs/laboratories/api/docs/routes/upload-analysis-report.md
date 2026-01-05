---
title: Téléverser le rapport final
parent: Routes
nav_order: 3
---

## Téléverser le rapport final

**`POST /api/labs/orders/{orderId}/report`**

Permet de téléverser le rapport PDF final d’une commande, une fois toutes les analyses terminées.

**Paramètres d’URL**

- `{orderId}` : Identifiant de la commande.

### Corps de la requête

Payload **multipart/form-data**

- `file` : Fichier PDF du rapport final
