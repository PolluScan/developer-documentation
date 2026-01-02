### 2.Authentification requise

Toutes les requêtes API vers ces endpoints doivent être authentifiées.  
Vous devez inclure un jeton Bearer dans l’en-tête `Authorization` de chaque requête.  
Ce jeton agit comme votre clé API et permet de vérifier votre identité ainsi que vos autorisations.

**Format de l’en-tête :**

X-Lab-Id: <lab_id>

curl -X POST exmpleApi.ch/api\
  -H "Content-Type: application/json" \
  -H "X-Lab-Id: lab_id" \
  -d '{"status":"received"}'

```yaml
Authorization: Bearer VOTRE_API_KEY
```

⚠️ **Attention** : Si une requête est envoyée sans clé API valide, elle sera rejetée.

### Index

1. [Configuration](./setup.md)
3. [Routes API disponibles](./apiRoutes.md)
   - [Mettre à jour le statut des commandes](./routes/updateOrderStatus.md)
   - [Soumettre les résultats d'analyse des échantillons](./routes/addAnalysisResults.md)
   - [Téléverser les rapports PDF finaux](./routes/uploadAnalysisReport.md)
   - [Consulter la liste des commandes en attente](./routes/fetchOpenOrders.md)
4. [Support](../support.md)