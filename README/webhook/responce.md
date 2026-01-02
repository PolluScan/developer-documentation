#### Réception

```yaml
2xx: OK — la commande est marquée comme reçue
Autres: FAIL
```

## ⚠️ ATTENTION IMPORTANTE : Gestion des réponses HTTP

En cas d'échec, notre système **réessaiera périodiquement jusqu'à obtenir un code de statut HTTP 2xx**.

### Instructions CRITIQUES :

1. **Votre endpoint DOIT retourner un code de statut 2xx** pour indiquer que la requête a été traitée avec succès
2. **Tout autre code de statut** (4xx, 5xx, etc.) sera considéré comme un échec et déclenchera de nouvelles tentatives
3. **Même une réponse 500 ou 404** entraînera des réessais automatiques

### Index

1. [Configuration](./setup.md)
2. [Réception d'une nouvelle commande](./new-order.md)
3. [Support](../support.md)