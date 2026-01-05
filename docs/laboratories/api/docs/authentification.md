---
title: Authentification
parent: API Laboratoires
nav_order: 2
---

## Authentification requise

Toutes les requêtes API vers ces endpoints doivent être authentifiées.  
Vous devez inclure un jeton Bearer dans l’en-tête `Authorization` de chaque requête.  
Ce jeton agit comme votre clé API et permet de vérifier votre identité ainsi que vos autorisations.

**Format de l’en-tête :**

`X-Lab-Id: <lab_id>`

```bash
curl -X POST exmpleApi.ch/api\
 -H "Content-Type: application/json" \
 -H "X-Lab-Id: lab_id" \
 -d '{"status":"received"}'
```

```yaml
Authorization: Bearer VOTRE_API_KEY
```

⚠️ **Attention** : Si une requête est envoyée sans clé API valide, elle sera rejetée.
