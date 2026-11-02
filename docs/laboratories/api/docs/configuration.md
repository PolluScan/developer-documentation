---
title: Configuration
parent: API Laboratoires
nav_order: 1
---

## Récupérez la clé API

### Accéder à l'espace développeur

1. Connectez-vous à votre compte PolluScan sur <a href="https://admin.polluscan.ch" target="_blank">admin.polluscan.ch</a>
2. Dans le menu principal, rendez-vous sur la page /developer
3. Cliquez sur Créer une clé API

![Get Your API]({{ '/assets/images/api-key.png' | relative_url }})

Nommez la clé API générée et assurez-vous de la sauvegarder. Elle ne sera plus accessible après coup.

## Déclarez le schéma de résultat de chaque service

Chaque service d’analyse doit porter un schéma de résultat : il décrit le résultat d’un échantillon que votre système envoie à PolluScan (voir [Schéma de résultat]({{ '/docs/laboratories/api/docs/result-schema.html' | relative_url }})).

1. Connectez-vous à votre compte PolluScan sur <a href="https://admin.polluscan.ch" target="_blank">admin.polluscan.ch</a>
2. Dans le menu principal, rendez-vous sur la page /services
3. Ouvrez le service concerné et collez le schéma JSON dans le champ du schéma de résultat
4. Vérifiez l’aperçu : il affiche `examples[0]` tel que le diagnostiqueur le verra, avec le nombre de propriétés sans libellé ou sans unité
5. Enregistrez. Un schéma non conforme est refusé avec la liste de toutes les violations (`ERROR_INVALID_RESULT_SCHEMA`)

⚠️ **Attention** : un service sans schéma de résultat ne peut pas recevoir de résultats. L’envoi est refusé avec `409 ERROR_SERVICE_NO_RESULT_SCHEMA` (voir [Soumettre les résultats d’analyse]({{ '/docs/laboratories/api/docs/routes/add-analysis-results.html' | relative_url }})).

Modifier le schéma d’un service n’affecte pas les commandes qui ont déjà reçu une première livraison de résultats : elles restent validées contre la version copiée lors de cette livraison.
