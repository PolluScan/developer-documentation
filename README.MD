# 📘 PolluScan – Documentation Développeur

Cette documentation explique comment intégrer la plateforme PolluScan
à votre système via **API sécurisée** et **webhooks**.


## 👥 À qui s’adresse cette documentation ?

Cette documentation est destinée :
- aux **laboratoires partenaires**
- aux **équipes techniques** en charge des intégrations
- aux **éditeurs de logiciels** connectés à PolluScan

## 🚀 Ce que vous pouvez faire

Grâce à l’intégration PolluScan, vous pouvez :

- recevoir automatiquement les commandes de diagnostic (webhooks)
- suivre l’état des analyses
- transmettre les résultats d’analyse
- déposer les rapports finaux des analyses(PDF)


## ⚡ Démarrage rapide

1. Générez une **clé API** depuis `/developer`
2. Configurez votre **endpoint webhook**
3. Recevez une commande PolluScan
4. Traitez l’analyse
5. Envoyez les résultats via l’API

## 🔐 Authentification

Toutes les requêtes API PolluScan nécessitent :
- une clé API (Bearer token)
- un identifiant laboratoire (`X-Lab-Id`)


👉 Commencez par lire l’une des sections suivantes :

- 🔐 **[Documentation API](./README/lab-api.md)**
  - Génération de clé API
  - Authentification
  - Endpoints
  - Exemples de requêtes

- 🔔 **[Documentation Webhooks](./README/webhooks.md)**
  - Configuration du webhook
  - Réception des commandes
  - Payload envoyé
  - Bonnes pratiques