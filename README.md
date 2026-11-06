# Dashboard Maintenance Curative & Préventive — Carrefour 2026

Tableau de bord de pilotage de la **maintenance curative et préventive** des magasins (Hyper, Market, Match) pour la Direction Technique France.

Il permet de suivre la performance des **prestataires**, des **magasins** et des **régions** à partir des exports ServiceNow : taux de clôture, délais, devis, budget, NPS, préventif, alertes, relances et rapports.

> 🔒 **Données traitées localement** : les fichiers sont lus et calculés directement dans le navigateur. Aucune donnée n'est envoyée sur un serveur, sauf si l'on utilise volontairement l'intégration Google Apps Script (chargement automatique, envoi d'e-mails).

---

## 1. Contenu du dépôt

| Fichier | Rôle |
|---|---|
| `index.html` | Application complète (HTML + CSS + JavaScript) en un seul fichier |
| `carrefour.png` | Logo affiché sur l'écran d'accueil et dans l'en-tête |

Bibliothèques chargées depuis un CDN :
- [SheetJS (xlsx 0.18.5)](https://sheetjs.com/) : lecture des fichiers Excel / CSV
- [Chart.js 3.9.1](https://www.chartjs.org/) : graphiques

Il n'y a **aucune installation ni compilation**. Il suffit d'ouvrir `index.html` dans un navigateur récent (Chrome ou Edge recommandés) ou de le publier via GitHub Pages.

---

## 2. Démarrage rapide

1. Ouvrir `index.html` (ou l'URL GitHub Pages du dépôt).
2. Sur l'écran d'accueil, choisir la vue : **🌍 Vue Nationale** ou une **région**.
3. Charger les données de l'une des deux façons suivantes :
   - **Manuellement** : glisser-déposer les fichiers Excel/CSV dans la zone prévue (ou cliquer sur *📁 Sélectionner les fichiers*), puis cliquer sur **✓ Charger le dashboard**.
   - **Automatiquement** : cliquer sur **⚡ Charger les données automatiquement** si une URL Apps Script est configurée (voir § 6).
4. Naviguer entre les onglets et utiliser les filtres du bandeau supérieur.
5. **↺ Nouveau fichier** réinitialise le tableau de bord pour charger d'autres exports.

---

## 3. Fichiers d'entrée

| Fichier | Obligatoire | Reconnu si le nom contient… |
|---|---|---|
| 📋 **WO Curatif National** (ordres de travail) | ✅ Oui | `curatif` ou `wo` |
| 📄 **QUO National** (devis) | ✅ Oui | `quo` ou `devis` |
| 🔧 **WO Préventif National** | Optionnel | `prev`, `préventif` ou `preventif` |
| ⭐ **NPS National** (satisfaction) | Optionnel | `nps` |

Formats acceptés : `.xlsx`, `.xls`, `.csv`. Pour les CSV, le séparateur `,` ou `;` est détecté automatiquement, ainsi que l'encodage (UTF-8 ou Latin-1).
Les fichiers dont le nom n'est pas reconnu sont affectés dans l'ordre : WO → QUO → NPS → Préventif.

### Colonnes utilisées

**WO curatif / préventif**
- `Numéro`, `État`, `Priorité`, `Créé`, `Fin de tâche`
- `Groupe d'affectation` : `FR_EXT_…` = prestataire externe (le nom du prestataire est extrait du code), tout autre groupe = équipe interne
- `Compte` (magasin), `Region` / `Région`, `Type de client` (Hyper / Market / Match)
- Préventif : `Titre de la demande`, `Équipement`

**QUO (devis)**
- `Statut du devis`, `Coût total` (colonne détectée automatiquement), `Créé`
- `Sous Famille d'Équipement`, `Bons de commande référencé`
- `Groupe d'affectation`, `Compte`, `Region`, `Type de client`

**NPS**
- `Valeur` (note de 0 à 10), `Créé`, `Groupe d'affectation`, `Compte`, `Region`

> Les en-têtes dont les accents sont abîmés par l'export ServiceNow (caractère `�`) sont tolérés : la correspondance des colonnes et des statuts se fait de manière approximative.

### Statuts normalisés

| WO | Code | Devis | Code |
|---|---|---|---|
| Clôturé achevé | CL | Approuvé ERP / Approuvé | APP |
| Assignée | AS | En attente d'approbation (DA) | AT / ATDA |
| En cours | EC | Ouvert / Assigné / En cours | OUV / AS / EC |
| En attente d'approbation | AT | Clôturé achevé | CL |
| Annulé | AN | Annulé | ANN |

Priorités : `1 - Très urgent` (TU), `2 - Urgent` (UR), `3 - Standard` (ST).

---

## 4. Filtres globaux

Le bandeau supérieur filtre la plupart des onglets par :
**région**, **type de magasin** (Hyper / Market / Match), **prestataire**, **magasin**, **CMM**, **mois** ou **plage de dates**, et **affectation** (externe / interne).

Une **recherche globale** (🔍 en haut à droite) retrouve un n° de WO ou de devis, un magasin ou un prestataire.

> Les onglets **Évolution**, **Fiabilité**, **Score** et **Comparatif N-1** ont leurs **propres filtres**, indépendants du bandeau.

---

## 5. Les onglets

| Onglet | Ce qu'il montre |
|---|---|
| 📊 **Vue générale** | KPI de synthèse (volumes, taux de clôture, WO en attente, coût des devis), répartition par statut, priorité et type, évolution mensuelle |
| 📈 **Taux clôture prestataire** | Classement des prestataires (min. 10 WO), taux par région et évolution mensuelle |
| 🏅 **Score prestataire** | Note /100 par prestataire pour l'aide à la décision (voir § 5.1), fiche PDF |
| 🏪 **Score magasin** | Note /100 par magasin (internes + prestataires) pour cibler les revues avec les CMM, fiches PDF |
| 🏪 **Taux clôture magasin** | Top 20 des magasins avec WO en attente, pourcentage en attente par région, actions requises |
| 📄 **Devis** | Coût mensuel (k€), top sous-familles et prestataires par coût, devis approuvés ERP |
| 💰 **Budget** | Saisie des budgets annuels par région, comparaison engagé / budget, écarts |
| 🔁 **Récurrence** | Top 20 des familles et équipements par volume de WO et par coût cumulé ; équipements à interventions répétées (réparer ou remplacer) |
| 📆 **Comparatif N-1** | Comparaison d'une période avec l'année précédente (volumes WO, dépenses, sous-familles) |
| 📉 **Évolution** | Courbes hebdomadaires reconstituées et historique des scores figés chaque semaine |
| 🧪 **Fiabilité** | Pannes répétées, fragilité par site, effet du préventif sur le curatif |
| ⚠ **Sans BC** | Risque financier : devis approuvés ERP **sans bon de commande** (par prestataire, par région, détail) |
| ⭐ **NPS** | Distribution des notes 0→10, NPS mensuel, top et flop des prestataires et magasins par type |
| 🔧 **Préventif** | Suivi des WO préventifs par statut, prestataire et type de magasin, préventifs en attente d'approbation |
| ⚠️ **Alertes** | Alertes opérationnelles : WO très urgents, WO en attente d'approbation, devis en attente, par magasin, prestataire et région |
| 🔔 **Relances** | Préparation des mails de relance (CMM / RPM ou prestataires) avec synthèse PDF et Excel : aperçu, test, puis confirmation |
| 📊 **GT Opex** | Indicateurs opérationnels Hyper / Market / Match filtrables, export PDF personnalisé |
| 📋 **Rapports** | Rapport consolidé PDF ou Excel par groupe de prestataires, magasin ou région |
| 🧹 **Qualité données** | Contrôles automatiques (WO, devis, rattachement CMM/RPM, contacts) qui pourraient fausser un score ou bloquer un envoi, export Excel |
| 📧 **Contacts** | Annuaire des interlocuteurs prestataires et groupes de diffusion |

Un clic sur une barre ou une ligne affiche généralement le **détail des WO** correspondants. Le bouton 🔧 de l'en-tête ouvre un **diagnostic** des données chargées.

### 5.1 Calcul du score prestataire

Chaque prestataire (avec un nombre minimal de WO sur la période, 20 par défaut) reçoit, pour chaque critère, une **note de rang de 0 à 100 par rapport aux autres prestataires**. Le score est la moyenne pondérée de ces notes :

| Critère | Sens | Poids par défaut |
|---|---|---|
| Taux de clôture | ↑ mieux | 30 |
| Taux de réintervention à 30 jours | ↓ mieux | 15 |
| % de WO clôturés en 7 jours ou moins | ↑ mieux | 10 |
| WO en cours ou assignés depuis 30 jours ou plus (pour 100 WO) | ↓ mieux | 20 |
| Délai moyen de clôture (jours) | ↓ mieux | 15 |
| NPS (min. 5 évaluations) | ↑ mieux | 10 |

Lecture : **≥ 75 = Bon**, **50 à 75 = À surveiller**, **< 50 = Critique**. Les poids sont modifiables dans l'onglet.

### 5.2 Règles de calcul principales

- **Délai de clôture** = `Fin de tâche` − `Créé` (WO clôturés uniquement, valeurs comprises entre 0 et 730 jours).
- **Âge d'un WO** = nombre de jours depuis `Créé` pour les WO non clôturés et non annulés.
- **Seuils d'attente** : WO en attente depuis **30 jours** ou plus, devis en attente depuis **7 jours** ou plus.
- **NPS** = % promoteurs (notes 9 et 10) − % détracteurs (notes 0 à 6).

---

## 6. Intégration Google Apps Script (optionnelle)

Une Web App Google Apps Script peut être reliée au dashboard. Son URL se saisit sur l'écran d'accueil (*⚙ URL Apps Script*). Elle permet :

| Fonction | Action appelée |
|---|---|
| Chargement automatique des données (pipeline mis à jour **chaque lundi à 7 h**) | `getData`, `getMeta` |
| Bouton **🔄 Mettre à jour** (recherche des nouveaux fichiers dans les mails ou le Drive) | `majDrive` |
| Envoi des e-mails de relance et de rapports, suivi des envois | `statutEnvoi` |
| Test de connexion | `ping` |

### Mode administrateur

Les envois d'e-mails, la mise à jour Drive et la synchronisation de la configuration partagée (contacts, groupes, objectifs) sont **réservés à l'administrateur** :
1. Ouvrir le dashboard avec `?admin=1` à la fin de l'URL.
2. Saisir le jeton généré par la fonction `definirTokenAdmin()` côté Apps Script (à faire une seule fois par navigateur).

### Liens régionaux

Le bouton **🔗 Liens régionaux** génère des liens de la forme `index.html?region=<REGION>`, qui ouvrent directement le dashboard sur une région. Ces liens peuvent être partagés avec les équipes régionales.

---

## 7. Données conservées dans le navigateur

Certains réglages sont enregistrés dans le `localStorage` du navigateur, et sont donc propres à chaque poste :

| Clé | Contenu |
|---|---|
| `mc_script_url` | URL de la Web App Apps Script |
| `mc_admin_token` | Jeton administrateur |
| `mc_contacts`, `mc_groupes` | Contacts et groupes de prestataires |
| `mc_env_obj`, `mc_env_obj_p` | Objectifs utilisés dans les fiches et envois prestataires |
| `mc_envois` | Historique des envois |
| `mc_theme` | Thème clair ou sombre |

> Vider les données du navigateur efface ces réglages. Pour l'administrateur, ils sont synchronisés avec Google Sheets via Apps Script.

---

## 8. Mise à jour mensuelle ou hebdomadaire (procédure type)

1. Extraire depuis ServiceNow les exports **WO curatif**, **QUO**, et si besoin **WO préventif** et **NPS** (périmètre national).
2. Nommer les fichiers de façon reconnaissable, par exemple `WO_curatif_2026-11.xlsx`, `QUO_2026-11.xlsx`, `WO_preventif_2026-11.xlsx`, `NPS_2026-11.xlsx`.
3. Les charger dans le dashboard, ou laisser le pipeline Apps Script les déposer automatiquement.
4. Vérifier l'onglet **🧹 Qualité données** avant de diffuser les scores ou d'envoyer des relances.
5. Générer les rapports (onglet **📋 Rapports**) et les relances (onglet **🔔 Relances**).

---

## 9. Dépannage

| Symptôme | Piste |
|---|---|
| Le bouton « Charger le dashboard » n'apparaît pas | Les fichiers **WO** et **QUO** sont tous deux obligatoires |
| Des statuts tombent en « Autre » | Vérifier les libellés de la colonne `État` ; utiliser le diagnostic 🔧 |
| Coûts des devis à 0 | La colonne `Coût total` n'a pas été trouvée ; vérifier l'en-tête de l'export |
| Onglet NPS vide | Le fichier NPS n'a pas été chargé (il est optionnel) |
| « Réservé à l'admin » | Ouvrir avec `?admin=1` et saisir le jeton (voir § 6) |
| Graphiques absents | Vérifier l'accès à `cdnjs.cloudflare.com` (réseau ou proxy) |
