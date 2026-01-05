---
title: Routes
parent: API Laboratoires
nav_order: 3
---

## Routes

Bienvenue dans la documentation de l'API PolluScan destinée aux laboratoires partenaires. Cette interface vous permet d'interagir programmatiquement avec la plateforme PolluScan pour gérer les commandes, soumettre des résultats et suivre l'état des analyses.

### 📋 Vue d'ensemble

L'API PolluScan fournit un ensemble d'endpoints RESTful sécurisés qui permettent aux laboratoires.

- [Mettre à jour le statut des commandes](./routes/updateOrderStatus.md)
- [Soumettre les résultats d'analyse des échantillons](./routes/addAnalysisResults.md)
- [Téléverser les rapports PDF finaux](./routes/uploadAnalysisReport.md)
- [Consulter la liste des commandes en attente](./routes/fetchOpenOrders.md)

### 🌐 URL de base de l'API

**URL de production :** `https://api.polluscan.ch/api`

Tous les endpoints sont relatifs à cette URL de base.
