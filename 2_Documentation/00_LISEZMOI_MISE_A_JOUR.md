# Observatoire des quartiers prioritaires : guide de mise à jour

Action Logement Services · Direction des Financements Bailleurs et Collectivités (DFBC) · Pôle Process Outils et Méthodes (POM)
Kit établi le 7 octobre 2026. Ce guide permet à une personne qui n'a pas construit l'outil de le mettre à jour seule.

## 1. Ce que produit le kit

| Fichier (dossier `sortie/`) | Contenu | Commande |
| --- | --- | --- |
| `Observatoire_QPV_Action_Logement.html` | Outil complet, 13 DR, 5 onglets | `python mettre_a_jour.py` |
| `Observatoire_QPV_v0_<DR>.html` | Même outil limité à une DR, repères France conservés | `python mettre_a_jour.py 7 --dr "DR IDF"` |
| `journal_mise_a_jour.txt` | Totaux de contrôle de chaque passage | automatique |
| `controles_fiabilite.txt` | Comparaison de chaque donnée de l'outil à sa source (OK / ALERTE / INFO) | automatique (étape 8) |

Chaque fichier HTML est autonome : il s'ouvre dans Chrome ou Edge, sans installation. Seuls le fond de carte, la recherche d'adresse et les itinéraires utilisent internet (services publics de l'IGN).

## 2. Installation (une seule fois, 10 minutes)

1. Installer Python 3.10 ou plus récent (python.org, cocher « Add Python to PATH »).
2. Dans un terminal ouvert dans le dossier du kit : `pip install -r requirements.txt`
3. Installer DAX Studio (gratuit, daxstudio.org) pour extraire les données du modèle Power BI.

## 3. Organisation du dossier

| Dossier | Rôle | Qui le modifie |
| --- | --- | --- |
| `sources/` | Fichiers bruts : Insee, ANCT, IGN, Éducation nationale, BAN, exports Power BI | Vous, à chaque mise à jour |
| `scripts/` | Programmes Python numérotés par étape, `config.py` (tous les chemins), `mettre_a_jour.py` | Rarement |
| `intermediaire/` | Données préparées par les scripts (déjà remplies avec la version d'octobre 2026) | Les scripts |
| `front/` | Code de l'outil (pages, styles, textes) | Seulement pour changer un texte ou un millésime |
| `sortie/` | Fichiers HTML produits | Les scripts |
| `requetes_power_bi.dax` | Les 13 requêtes à lancer dans le modèle Power BI « FICHE BAILLEUR » | Si le modèle Power BI change |

## 4. Calendrier conseillé

| Données | Fréquence | Source | Étapes à relancer |
| --- | --- | --- | --- |
| Droits de réservation, POLARIS, consommation, flux, attributions | 2 fois par an | Modèle Power BI | 5 puis 1c, 7 |
| RPLS (parc social, étiquettes, vacance) | 1 fois par an (printemps) | Modèle Power BI | 1, 2, 3, 5, 7 |
| Indicateurs Insee par QPV | 1 fois par an (quand l'Insee publie) | insee.fr | 4, 7 |
| Contours QPV, communes, EPCI | À chaque réforme (liste QPV, fusions de communes) | ANCT, Etalab, IGN | Toutes |
| Gares, communes et entreprises | 1 fois par an | Modèle Power BI | 6, 7 |
| Annuaire des écoles | 1 fois par an (rentrée) | data.education.gouv.fr | 4c, 7 |
| Taux de chômage par zone d'emploi | 4 fois par an | insee.fr/fr/statistiques/1893230 | 6, 7 |

## 5. Mise à jour pas à pas

### Étape A : extraire les données Action Logement (Power BI)
1. Ouvrir le fichier Power BI « FICHE BAILLEUR » à jour dans Power BI Desktop.
2. Ouvrir DAX Studio, se connecter au modèle ouvert (Power BI / SSDT Model).
3. Pour chacune des 13 requêtes de `requetes_power_bi.dax` : coller la requête, choisir **Output > File (CSV)**, lancer, enregistrer sous le nom indiqué dans `sources/action_logement/` (gares : `sources/gares/`, communes : `sources/communes/`).
4. Vérifier que les noms de colonnes du modèle n'ont pas changé. Si DAX Studio signale une colonne inconnue, chercher son nouveau nom dans Power BI et corriger la requête.

Contrôle connu (octobre 2026) : RPLS 2024 = 5 371 976 logements dont 1 670 167 en QPV, 147 532 en F ou G, 114 043 vacants (MODE = 2) ; 149 318 familles logées en 2025. Sur la carte : 5 355 906 logements (16 027 n'ont pas de code commune dans le RPLS) et 149 044 familles (274 rattachées à un code commune sans contour).

### Étape B : mettre à jour les sources publiques (si nouvelle édition)
Le plus simple : le carnet Google Colab décrit dans `SOURCES_ET_RECUPERATION.md` (une cellule par source, liens officiels, sans installation). Détail manuel ci-dessous.
- Insee, données QPV : dézipper chaque fichier (estimations démographiques, revenus, DEFM, CAF, EDUC, DNB, SIDE, RPLS QPV) dans `sources/insee/` en gardant le nom du dossier. Si l'Insee change les noms de fichiers ou de colonnes, adapter les lignes `rd(...)` en tête de `etape4a_portrait_insee.py`.
- Population par commune et par QPV : `pop_reference_communales_QPV24.csv`, `pop_QPV24.csv`.
- Contours QPV (ANCT) : fichiers geojson dans `sources/qpv/` (un par territoire + le fichier France WGS84).
- Base Adresse Nationale (seulement pour les étapes 2 et 5b) : télécharger les fichiers `adresses-with-ids-XX.csv.gz` (adresse.data.gouv.fr/data/ban/adresses/latest/csv-with-ids/) dans `sources/ban/`. Environ 3 Go, non inclus dans le kit.
- Communes et EPCI : paquet npm `@etalab/decoupage-administratif` (communes.json, epci.json) dans `sources/cog/`.

### Étape C : lancer les calculs
Dans un terminal ouvert dans `scripts/` :

| Commande | Quand |
| --- | --- |
| `python mettre_a_jour.py` | Mise à jour courante (étapes 1, 4, 5, 6, 7, 8), environ 25 minutes |
| `python etape8_controles.py` | Contrôles de fiabilité seuls |
| `python mettre_a_jour.py 2 3` | Nouveau RPLS : géolocalisation des adresses, 30 à 60 minutes (BAN requise) |
| `python mettre_a_jour.py 5 1 7` | Seulement les droits de réservation |
| `python mettre_a_jour.py 7 --dr "DR AURA"` | Version de démonstration d'une DR |

Le script s'arrête au premier problème et dit quelle étape a échoué.

### Étape D : contrôler avant de diffuser
1. Lire `sortie/controles_fiabilite.txt` : aucune ligne « ALERTE » ne doit rester inexpliquée. Lire aussi `sortie/journal_mise_a_jour.txt` et comparer les totaux avec la version précédente. Un écart de plus de 10 % sur un total national demande une vérification.
2. Ouvrir le HTML et tester : un EPCI connu dans l'Atlas, un quartier dans le Portrait, une adresse dans l'onglet Adresse, une planche du Focus, les boutons Imprimer et CSV.
3. Vérifier la part nationale de la population en QPV (8,8 % en 2020) et le nombre de QPV (1 584 en 2024).

### Étape E : mettre à jour les textes et les millésimes
Les années citées dans l'outil (« RPLS 2024 », « attributions 2025 », « engagements 2018-2025 ») sont écrites dans le code de `front/`. Après une mise à jour, chercher l'ancienne année dans les fichiers de `front/` (Rechercher dans un éditeur comme Notepad++ ou VS Code) et la remplacer. Fichiers concernés : `methodes.html`, `page_body.html`, `app_adr.js`, `atlas_resa.js`, `atlas_focus.js`, `portrait.js`, `atlas_evol.js`, `atlas_export.js`, `app_shell.html`, `tips_*.js`. Pour les évolutions, changer aussi `ANNEES_ATTRIB` et `PERIODE_RESA` dans `etape5c_evolutions.py`.

## 6. Ce que fait chaque étape

| Étape | Script | Entrées | Sortie |
| --- | --- | --- | --- |
| 1a | `etape1a_atlas_geometries.py` | communes, arrondissements, QPV, population de référence | `data.json` |
| 1b | `etape1b_atlas_compacter.py` | `data.json` | `data_c.json` |
| 1c | `etape1c_atlas_indicateurs.py` | exports Power BI 1 et 2, population par QPV, points RPLS | `data_c.json` complété |
| 2 | `etape2_geocodage_rpls.py` | export Power BI 3, BAN, contours QPV | `rpls_fr_geo.json` |
| 3 | `build_points.py` | `rpls_fr_geo.json` | `rpls_pts.json` |
| 4a, 4b | `etape4a_portrait_insee.py`, `etape4b_portrait_communes.py` | fichiers Insee QPV | `qpv_portrait.json` |
| 4c | `etape4c_portrait_ecoles_bailleurs.py` | annuaire Éducation nationale, points RPLS, contours | `qpv_portrait.json` complété |
| 5a | `etape5a_reservations.py` | exports Power BI 4 à 7 | `resa.json` |
| 5b | `etape5b_programmes_reserves.py` | export Power BI 8, BAN | `ops_geo.json`, `ops.json` |
| 5c | `etape5c_evolutions.py` | exports Power BI 8 à 11 | `evol.json` |
| 6 | `etape6_reperes.py` | exports Power BI 12 et 13, contours QPV | `obs_small.json`, `qpv_wgs_simpl.json` |
| 6b | `etape6b_zones_emploi.py` | table communes -> zones d'emploi, taux de chômage (Insee) | `ze_geo.json` |
| 7a | `etape7a_assembler.py` | `front/` + `intermediaire/` | HTML complet |
| 7b | `etape7b_version_dr.py` | idem, filtré sur une DR | HTML d'une DR |
| 8 | `etape8_controles.py` | sources + `intermediaire/` | `controles_fiabilite.txt` |

Méthodes détaillées (formules, seuils, limites) : onglet Méthodes de l'outil, ou `front/methodes.html`.

## 7. Règles à ne pas perdre
- Les pourcentages sont toujours somme des numérateurs ÷ somme des dénominateurs, jamais une moyenne de taux.
- Durée d'écoulement : stock ÷ familles logées par an (moyenne 2023-2025). Au-delà de 30 ans ou sous 5 familles par an, l'outil affiche « Plus de 30 ans » (fonction DURF dans `front/app_adr.js` et `front/atlas_resa.js` ; copie de référence dans `front/durf.js`).
- Pôle d'emploi : commune de plus de 5 000 entreprises (`SEUIL` dans `etape6_reperes.py`).
- Programmes réservés : seuls ceux localisés à l'adresse ou à la voie sont placés sur la carte.
- Données Action Logement : usage interne uniquement.

## 8. En cas de problème

| Message | Cause probable | Solution |
| --- | --- | --- |
| `FileNotFoundError` | Un export manque ou porte un autre nom | Vérifier le nom exact dans `sources/` (voir `config.py`) |
| `KeyError` sur une colonne Insee | L'Insee a renommé une variable | Ouvrir le fichier `meta_...csv` et corriger le nom dans `etape4a_portrait_insee.py` |
| Totaux très différents de la version précédente | Filtre changé dans Power BI, export incomplet | Relancer la requête DAX, comparer avec les contrôles de l'étape A |
| L'outil s'ouvre sans carte | Pas d'accès internet aux tuiles IGN | Normal hors réseau : les chiffres restent justes |
| Fichier HTML de plus de 30 Mo trop lourd pour la messagerie | Données en hausse | Diffuser par lien (SharePoint) ou produire des versions par DR (étape 7b) |

## 9. Pistes non réalisées (octobre 2026)
- Demande de logement social (SNE) : à ajouter dès qu'un export par commune est disponible (Observatoire des territoires, jeu « pression_sne »).
- Quartiers NPNRU : marquer les sites dès réception de la liste.
- Temps de trajet en transports en commun : nécessite une clé d'accès à un calculateur (Navitia ou régional).
- Version en ligne (SharePoint ou serveur interne) pour éviter l'envoi d'un fichier de 31 Mo.
- `scripts/optionnel/build_idf.py` et `cap.js` : exemple de page de présentation d'une DR (Focus DR Île-de-France). Ils demandent Node.js et Playwright, et les textes sont propres à l'Île-de-France.
