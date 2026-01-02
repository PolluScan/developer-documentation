# Webhooks PolluScan

PolluScan ne fournit pas de webhooks natifs.  
En revanche, les **laboratoires partenaires définissent et gèrent leurs propres endpoints de webhook** afin de recevoir des notifications automatiques liées aux commandes.

PolluScan déclenche ces **webhooks configurés par les laboratoires** lorsque certains événements se produisent (par exemple, la création d’une nouvelle commande ou la mise à jour du statut d’une commande).

## Sommaire

1. [Configuration d’un webhook](./webhook/setup.md)
2. [Notification de nouvelle commande](./webhook/new-order.md)
3. [Format de réponse attendu](./webhook/response.md)
4. [Support](./support.md)