### Lister toutes les commandes ouvertes

**`GET /api/labs/orders/open`**

Cet endpoint permet de récupérer toutes les commandes en attente.

#### Exemple de réponse

```typescript
{
  id: string; // Identifiant unique de la commande
  created_at: string; // Date et heure de création (format ISO 8601 UTC, ex: "2025-07-04T17:40:35.171+00:00")
  updated_at: string; // Date et heure de dernière mise à jour (format ISO 8601)
  status: string; // Statut actuel de la commande (voir section "Statuts disponibles")

  count: number; // Nombre d'échantillons à analyser
  sent_at: string; // Date d’envoi du colis (format ISO 8601)
  parcel_id: string; // Numéro de tracking renseigné par le technicien
  url_tracker: string; // URL du service de suivi logistique
  logistic_company: string; // Nom de l’entreprise de transport utilisée

  received_at: string | null; // Date de réception par le laboratoire
  received_by: string | null; // Identifiant ou nom de la personne ayant confirmé la réception

  lost_reason: string | null; // Raison de la perte, si applicable
  lost_by: string | null; // Identifiant ou nom de la personne ayant signalé la perte

  analysis_started_at: string | null; // Date de début d’analyse
  analysis_started_by: string | null; // Utilisateur ayant lancé l’analyse

  analysis_finalized_at: string | null; // Date de fin d’analyse
  analysis_finalized_by: string | null; // Utilisateur ayant terminé l’analyse

  result_document: string | null; // URL ou identifiant du rapport PDF final
  results_available_at: string | null; // Date à laquelle les résultats sont disponibles
  results_available_by: string | null; // Utilisateur ayant mis les résultats à disposition
  results_confirmed_at: string | null; // Date de confirmation des résultats
  results_confirmed_by: string | null; // Utilisateur ayant confirmé les résultats

  diagnostic: {
    identifier: string; // Identifiant unique du diagnostic
    name: string; // Nom du diagnostic
  };

  lab: {
    id: string; // Identifiant unique du laboratoire
    name: string; // Nom commercial du laboratoire
    admin: {
      legal_name: string; // Nom légal de l’entreprise
      vat_number?: string; // Numéro de TVA (optionnel)
      fiscal_number?: string; // Numéro fiscal (optionnel)
    };
  };

  lab_service: {
    id: string; // Identifiant unique du service demandé
    name: {
      lang: string; // Code langue (fr, en, de, it)
      text: string; // Nom du service
    }[];
    price: {
      currency: string; // Devise du prix (ex: "CHF", "EUR")
      amount: number; // Montant du service
    }[];
    time_limit: number; // Délai de traitement (en jours)
    granularity: string; // Niveau de détail (ex: "sample", "batch")
    available_until: string; // Date de disponibilité du service
  };

  lab_address: {
    id: string; // Identifiant unique de l’adresse du labo
    name: string; // Nom du site ou de l’établissement
    address: {
      first_line: string; // Ligne d’adresse principale
      second_line: string; // Ligne d’adresse complémentaire
      post_code: string; // Code postal
      city: string; // Ville
      country: string; // Pays (ISO 3166-1 alpha-2)
    };
  };

  created_by: {
    name: {
      surname: string; // Nom
      name: string; // Prénom
    };
    email: string; // Adresse e-mail
    phone: {
      country_code: string; // Indicatif du pays (ex: "41" pour la Suisse)
      number: string; // Numéro de téléphone
    };
    language: string; // Langue utilisée (fr, en, de, it)
    title: string; // Titre professionnel
    organization: {
      name: string; // Nom de l’organisation ou entreprise
      address: {
        first_line: string;
        second_line: string;
        post_code: string;
        city: string;
        country: string;
      };
      contact: {
        email: string; // Adresse e-mail du contact principal
        phone: string; // Téléphone du contact principal
        website: string; // Site web (si disponible)
      };
    };
  };

  sent_by: {
    name: {
      surname: string;
      name: string;
    };
    email: string;
    phone: {
      country_code: string;
      number: string;
    };
    language: string;
    title: string;
  };

  samples_data: {
    id: string; // Identifiant unique de l’échantillon
    qr: {
      id: string; // Nom visible de l’échantillon (ex: code sur le sachet)
      date: string; // Date du prélèvement ou du scan
      location: {
        lat: number; // Latitude du prélèvement
        long: number; // Longitude du prélèvement
      };
    };
    pollutant_tested: string; // Clé du polluant testé (selon référentiel PolluScan)
    material_type: string; // Clé du matériau testé (selon référentiel PolluScan)
  }[];
}[]
```
### Index

- [Mettre à jour le statut des commandes](./routes/updateOrderStatus.md)
- [Soumettre les résultats d'analyse des échantillons](./routes/addAnalysisResults.md)
- [Téléverser les rapports PDF finaux](./routes/uploadAnalysisReport.md)