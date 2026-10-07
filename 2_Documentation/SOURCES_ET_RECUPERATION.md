# Sources de l'Observatoire QPV et code de récupération (Google Colab)

Action Logement Services · DFBC · POM · état au 7 octobre 2026

Ce document répond à deux questions pour la personne qui mettra l'outil à jour :

1. Où sont les fichiers déjà récupérés, et d'où viennent-ils ?
2. Comment les récupérer à nouveau, sans rien installer, avec Google Colab ?

Les données Action Logement (Power BI) ne sont pas concernées : elles s'extraient avec `requetes_power_bi.dax` (voir `00_LISEZMOI_MISE_A_JOUR.md`).

---

## 1. Fichiers déjà enregistrés

Tous les fichiers publics utilisés en octobre 2026 sont dans le kit, dossier `sources/` (archive `Kit_Observatoire_QPV_2_sources_publiques.zip`). Le géocodage des adresses RPLS est dans `Kit_Observatoire_QPV_3_geocodage.zip`.

| Dossier du kit | Fichier | Producteur | Millésime | Page officielle |
| --- | --- | --- | --- | --- |
| `sources/qpv/` | `QP2024_France_Hexagonale_Outre_Mer_WGS84.geojson` et un fichier par territoire | ANCT (SIG Ville) | Géographie prioritaire 2024 | https://www.data.gouv.fr/datasets/5a561801c751df42d7fca9b6 |
| `sources/insee/` | `pop_QPV24.csv`, `pop_reference_communales_QPV24.csv` | Insee | Population de référence QPV 2024 | https://www.insee.fr/fr/statistiques/8210600 et 8210602 |
| `sources/insee/estimations-demographiques_2022_QPV2024_csv/` | données QPV | Insee | 2022 | https://www.insee.fr/fr/statistiques/8742545 |
| `sources/insee/revenus_pauvrete_2021_qp24_v2_csv/` | données QPV | Insee | 2021 | https://www.insee.fr/fr/statistiques/8243167 |
| `sources/insee/DEFM2024_QP24_csv/` | demandeurs d'emploi | Insee / France Travail | 2024 | https://www.insee.fr/fr/statistiques/8644871 |
| `sources/insee/beneficiaires_CAF_31-12-2024_QP24/` | allocataires CAF | Insee / CNAF | 31/12/2024 | https://www.insee.fr/fr/statistiques/8682381 |
| `sources/insee/EDUC_2024_2025_QP24/` | scolarité | Insee / Éducation nationale | 2024-2025 | https://www.insee.fr/fr/statistiques/8994242 |
| `sources/insee/DNB_2025_QP24/` | brevet | Insee / Éducation nationale | 2025 | https://www.insee.fr/fr/statistiques/9026325 |
| `sources/insee/RPLS_01-01-2025_QPV_2024/` | parc social en QPV | Insee / SDES | 01/01/2025 | https://www.insee.fr/fr/statistiques/9054633 |
| `sources/insee/side_2023_QPV2024/` | entreprises (SIDE) | Insee | 2023 | https://www.insee.fr/fr/statistiques/8994268 |
| `sources/zones_emploi/ze2020.json` | communes → zones d'emploi (compacté) | Insee | ZE2020, communes au 01/01/2026 | https://www.insee.fr/fr/information/4652957 |
| `sources/zones_emploi/chomage_ze.json` | taux de chômage par zone d'emploi | Insee | 2e trimestre 2026 (publié le 18/09/2026) | https://www.insee.fr/fr/statistiques/1893230 |
| `sources/communes/communes_france_geoapi_simpl.geojson` | contours des communes | Etalab (API Découpage administratif) | 2026 | https://geo.api.gouv.fr |
| `sources/communes/arrondissements_PLM_geoapi.geojson` | arrondissements de Paris, Lyon, Marseille | Etalab | 2026 | https://geo.api.gouv.fr |
| `sources/cog/` | `communes_2022.json`, `communes_2026.json`, `epci_2026.json` | Etalab, paquet `@etalab/decoupage-administratif` | versions 2.3.0 et 6.0.0 | https://www.npmjs.com/package/@etalab/decoupage-administratif |
| `sources/education/fr-en-annuaire-education.csv` | écoles, collèges, lycées | Éducation nationale | rentrée 2026 | https://data.education.gouv.fr/explore/dataset/fr-en-annuaire-education |
| `sources/ban/` | adresses (non inclus, 3 Go) | Base Adresse Nationale | « latest » | https://adresse.data.gouv.fr/data/ban/adresses/latest/csv-with-ids/ |

Fichiers Action Logement (exports Power BI) : `sources/action_logement/`, `sources/gares/gares.csv`, `sources/communes/communes_coordonnees.csv`.

## 2. Services interrogés en direct par l'outil

Ces services ne produisent pas de fichier. L'outil les appelle au moment où l'utilisateur s'en sert. Ils sont gratuits, publics et sans clé. Ils demandent seulement que le poste ait accès à internet. Le réseau Action Logement doit laisser passer `data.geopf.fr`.

| Usage dans l'outil | Service | Adresse |
| --- | --- | --- |
| Fond de carte | IGN Géoplateforme, Plan IGN v2 (WMTS) | `https://data.geopf.fr/wmts` |
| Fond de secours | CARTO Voyager (OpenStreetMap) | `https://basemaps.cartocdn.com` |
| Recherche d'adresse | IGN Géoplateforme, géocodage | `https://data.geopf.fr/geocodage/search` et `/reverse` |
| Recherche de lieux (école, gare, restaurant) | Photon (OpenStreetMap) | `https://photon.komoot.io/api/` |
| Itinéraires voiture et à pied | IGN, itinéraire (moteur OSRM sur BD TOPO) | `https://data.geopf.fr/navigation/itineraire` |
| Zone atteignable en X minutes | IGN, isochrone (moteur Valhalla sur BD TOPO) | `https://data.geopf.fr/navigation/isochrone` |
| Zones d'activité | IGN, BD TOPO, couche `zone_d_activite_ou_d_interet` (WFS) | `https://data.geopf.fr/wfs/ows` |
| Zones d'activité (secours) | OpenStreetMap, Overpass | `https://overpass-api.de/api/interpreter` |

Requête testée le 7 octobre 2026 pour les zones d'activité, depuis le poste de l'utilisateur. Autour de la Porte des Alpes (Saint-Priest, Bron), elle renvoie 16 zones, dont le Parc Technologique de Lyon, le Multiparc de Parilly et la Zac du Champ du Pont :

```
https://data.geopf.fr/wfs/ows?SERVICE=WFS&VERSION=2.0.0&REQUEST=GetFeature
 &TYPENAMES=BDTOPO_V3:zone_d_activite_ou_d_interet&OUTPUTFORMAT=application/json&COUNT=1500
 &CQL_FILTER=nature IN ('Zone industrielle','Divers commercial') AND BBOX(geometrie,lat_min,lon_min,lat_max,lon_max)
```

Attention : dans `BBOX`, l'ordre est **latitude puis longitude**. Avec l'ordre inverse, le service renvoie zéro résultat sans erreur.

---

## 3. Récupérer les sources avec Google Colab

Ouvrir https://colab.research.google.com, créer un nouveau carnet, puis coller chaque bloc dans une cellule et l'exécuter dans l'ordre. Les fichiers sont écrits dans Google Drive, dossier `Observatoire_QPV/sources/`. On recopie ensuite ce dossier à la place de `sources/` dans le kit.

Il n'est utile de relancer qu'une cellule dont la source a changé (voir le calendrier du guide de mise à jour).

### Cellule 0 : préparation (toujours en premier)

```python
from google.colab import drive
drive.mount('/content/drive')

import os, re, io, json, zipfile, requests, pandas as pd
BASE = '/content/drive/MyDrive/Observatoire_QPV/sources/'
for d in ['qpv','insee','zones_emploi','communes','cog','education','ban','zones_activite']:
    os.makedirs(BASE + d, exist_ok=True)
H = {'User-Agent': 'Mozilla/5.0 (Observatoire QPV Action Logement)'}

def telecharger(url, chemin):
    r = requests.get(url, headers=H, timeout=300); r.raise_for_status()
    open(chemin, 'wb').write(r.content); print('OK', chemin, round(len(r.content)/1e6, 1), 'Mo')
    return chemin

def dezipper(url, dossier):
    r = requests.get(url, headers=H, timeout=300); r.raise_for_status()
    zipfile.ZipFile(io.BytesIO(r.content)).extractall(dossier); print('OK', dossier, os.listdir(dossier)[:6])

def fichiers_insee(page):
    """Liste les fichiers téléchargeables d'une page insee.fr (liens /fichier/)."""
    html = requests.get(page, headers=H, timeout=60).text
    liens = sorted(set(re.findall(r'(/fr/statistiques/fichier/[^"\']+\.(?:zip|xlsx|csv))', html)))
    return ['https://www.insee.fr' + l for l in liens]
```

### Cellule 1 : contours des QPV (ANCT)

Le jeu data.gouv.fr est interrogé par son API : on obtient la liste à jour des fichiers, sans dépendre d'un lien qui change.

```python
ds = requests.get('https://www.data.gouv.fr/api/1/datasets/5a561801c751df42d7fca9b6/', headers=H).json()
for r in ds['resources']:
    print(r['title'], '|', r['format'], '|', r['last_modified'][:10], '|', r['url'])
# Repérer dans la liste la ressource « QP2024 ... WGS84 » (zip ou geojson), puis :
# dezipper('<url de la ligne choisie>', BASE + 'qpv/')
```

### Cellule 2 : données Insee par quartier

Chaque page Insee contient un ou plusieurs zip. La cellule les télécharge tous et les dézippe dans un sous-dossier portant le nom du zip, comme dans le kit. Quand l'Insee publie un nouveau millésime, il suffit de remplacer le numéro de page, en le trouvant sur https://www.insee.fr/fr/statistiques/8186144.

```python
PAGES = {
    'population QPV':            'https://www.insee.fr/fr/statistiques/8210600',
    'population communale QPV':  'https://www.insee.fr/fr/statistiques/8210602',
    'estimations démographiques':'https://www.insee.fr/fr/statistiques/8742545',
    'revenus et pauvreté':       'https://www.insee.fr/fr/statistiques/8243167',
    'demandeurs d emploi':       'https://www.insee.fr/fr/statistiques/8644871',
    'allocataires CAF':          'https://www.insee.fr/fr/statistiques/8682381',
    'scolarité':                 'https://www.insee.fr/fr/statistiques/8994242',
    'brevet':                    'https://www.insee.fr/fr/statistiques/9026325',
    'parc social RPLS':          'https://www.insee.fr/fr/statistiques/9054633',
    'entreprises SIDE':          'https://www.insee.fr/fr/statistiques/8994268',
}
for nom, page in PAGES.items():
    for url in fichiers_insee(page):
        f = url.split('/')[-1]
        if f.endswith('.zip'):
            dezipper(url, BASE + 'insee/' + f[:-4])
        elif f.endswith('.csv'):
            telecharger(url, BASE + 'insee/' + f)
# Les deux fichiers de population sont attendus à plat dans insee/ :
import glob, shutil
for f in glob.glob(BASE + 'insee/*/pop_QPV24.csv') + glob.glob(BASE + 'insee/*/pop_reference_communales_QPV24.csv'):
    shutil.copy(f, BASE + 'insee/')
```

### Cellule 3 : zones d'emploi et taux de chômage (Insee)

À relancer chaque trimestre pour le chômage. La cellule produit directement les deux fichiers lus par `etape6b_zones_emploi.py`.

```python
# a) Table communes -> zone d'emploi 2020 (prendre le millésime de communes le plus récent)
url = [u for u in fichiers_insee('https://www.insee.fr/fr/information/4652957') if re.search(r'ZE2020_au_01-01-\d{4}', u)][-1]
print('table utilisée :', url)
z = zipfile.ZipFile(io.BytesIO(requests.get(url, headers=H).content))
x = [n for n in z.namelist() if n.lower().endswith('.xlsx')][0]
t = pd.read_excel(z.open(x), sheet_name=0, skiprows=5, dtype=str)
t = t[['CODGEO','ZE2020','LIBZE2020']].dropna()
zones = sorted(set(zip(t.ZE2020, t.LIBZE2020)))
idx = {c: i for i, (c, n) in enumerate(zones)}
def b36(n): s='0123456789abcdefghijklmnopqrstuvwxyz'; return s[n//36] + s[n%36]
compact = ''.join(c.zfill(5) + b36(idx[z]) for c, z in zip(t.CODGEO, t.ZE2020))
json.dump({'Z': [[c, n] for c, n in zones], 'C': compact}, open(BASE + 'zones_emploi/ze2020.json', 'w'), ensure_ascii=False)
print(len(zones), 'zones,', len(t), 'communes')

# b) Taux de chômage trimestriel : dernier trimestre et même trimestre un an avant
url = [u for u in fichiers_insee('https://www.insee.fr/fr/statistiques/1893230') if re.search(r'chomage-zone-t1-2003-t\d-\d{4}\.xlsx', u)][0]
print('fichier utilisé :', url)
xl = pd.ExcelFile(io.BytesIO(requests.get(url, headers=H).content))
brut = pd.read_excel(xl, sheet_name=xl.sheet_names[0], header=None, dtype=object)
ligne = brut.index[brut.astype(str).apply(lambda r: r.str.fullmatch(r'ZE2020|Code|CODGEO|ze2020', case=False).any(), axis=1)][0]
d = pd.read_excel(xl, sheet_name=xl.sheet_names[0], header=ligne)
col_code = [c for c in d.columns if str(c).strip().lower() in ('ze2020', 'code', 'codgeo')][0]
trim = [c for c in d.columns if re.search(r'\d{4}', str(c))]
dernier, un_an_avant = trim[-1], trim[-5]
print('dernier trimestre :', dernier, '| un an avant :', un_an_avant)   # vérifier visuellement
out = [[str(r[col_code]).zfill(4), float(r[dernier]), float(r[un_an_avant])] for _, r in d.iterrows()
       if re.fullmatch(r'\d{3,4}', str(r[col_code])) and pd.notna(r[dernier])]
json.dump(out, open(BASE + 'zones_emploi/chomage_ze.json', 'w'))
print(len(out), 'zones avec un taux')
# Puis, dans le kit : changer PERIODE en tête de scripts/etape6b_zones_emploi.py (ex. '3e trimestre 2026').
```

### Cellule 4 : contours des communes et arrondissements (API Découpage administratif)

```python
telecharger('https://geo.api.gouv.fr/communes?fields=code,nom,population&format=geojson&geometry=contour',
            BASE + 'communes/communes_france_geoapi.geojson')
telecharger('https://geo.api.gouv.fr/communes?type=arrondissement-municipal&fields=code,nom,population&format=geojson&geometry=contour',
            BASE + 'communes/arrondissements_PLM_geoapi.geojson')

# Simplification (le kit utilise une version allégée) :
!pip -q install shapely
from shapely.geometry import shape, mapping
g = json.load(open(BASE + 'communes/communes_france_geoapi.geojson'))
for f in g['features']:
    f['geometry'] = mapping(shape(f['geometry']).simplify(0.0005, preserve_topology=True))
json.dump(g, open(BASE + 'communes/communes_france_geoapi_simpl.geojson', 'w'))
```

### Cellule 5 : listes officielles des communes et EPCI (code officiel géographique)

```python
V = '6.0.0'   # dernière version sur https://www.npmjs.com/package/@etalab/decoupage-administratif
telecharger(f'https://unpkg.com/@etalab/decoupage-administratif@{V}/data/communes.json', BASE + 'cog/communes_2026.json')
telecharger(f'https://unpkg.com/@etalab/decoupage-administratif@{V}/data/epci.json',     BASE + 'cog/epci_2026.json')
# communes_2022.json (populations légales 2020) reste celui du kit : version 2.3.0 du même paquet.
```

### Cellule 6 : annuaire des écoles, collèges, lycées (Éducation nationale)

```python
url = ('https://data.education.gouv.fr/api/explore/v2.1/catalog/datasets/fr-en-annuaire-education/exports/csv'
       '?delimiter=%3B&select=identifiant_de_l_etablissement,nom_etablissement,type_etablissement,statut_public_prive,'
       'code_commune,libelle_nature,appartenance_education_prioritaire,latitude,longitude,etat')
telecharger(url, BASE + 'education/fr-en-annuaire-education.csv')
print(pd.read_csv(BASE + 'education/fr-en-annuaire-education.csv', sep=';', nrows=3))
```

### Cellule 7 : Base Adresse Nationale (seulement pour un nouveau RPLS)

Environ 3 Go au total, soit 10 à 20 minutes. Colab gratuit dispose d'environ 100 Go de disque temporaire.

```python
deps = [f'{i:02d}' for i in range(1, 96) if i != 20] + ['2A', '2B', '971', '972', '973', '974', '976']
for d in deps:
    f = f'adresses-with-ids-{d}.csv.gz'
    try:
        telecharger(f'https://adresse.data.gouv.fr/data/ban/adresses/latest/csv-with-ids/{f}', BASE + 'ban/' + f)
    except Exception as e:
        print('ÉCHEC', d, e)
```

### Cellule 8 : instantané national des zones d'activité (IGN BD TOPO)

L'outil lit ces zones en direct. Cette cellule en garde une copie datée, utile pour archiver l'état d'une année ou pour travailler hors ligne. Les zones sont interrogées par blocs de 5 000, environ 2 à 5 minutes.

```python
W = 'https://data.geopf.fr/wfs/ows'
filtre = "nature IN ('Zone industrielle','Divers commercial')"
feats, start = [], 0
while True:
    p = {'SERVICE':'WFS','VERSION':'2.0.0','REQUEST':'GetFeature','TYPENAMES':'BDTOPO_V3:zone_d_activite_ou_d_interet',
         'OUTPUTFORMAT':'application/json','COUNT':5000,'STARTINDEX':start,'SORTBY':'cleabs','CQL_FILTER':filtre,
         'PROPERTYNAME':'cleabs,nature,nature_detaillee,toponyme,commune,insee_commune,geometrie'}
    j = requests.get(W, params=p, headers=H, timeout=300).json()
    lot = j.get('features', [])
    feats += lot; print(start, '->', len(feats), '/', j.get('numberMatched'))
    if len(lot) < 5000: break
    start += 5000
from datetime import date
nom = BASE + f'zones_activite/zones_activite_bdtopo_{date.today():%Y-%m-%d}.geojson'
json.dump({'type':'FeatureCollection','features':feats}, open(nom, 'w'), ensure_ascii=False)
print('OK', nom, len(feats), 'zones')
print(pd.Series([f['properties']['nature'] for f in feats]).value_counts())
```

### Cellule 9 : contrôle rapide de ce qui a été récupéré

```python
for racine, _, fichiers in os.walk(BASE):
    for f in fichiers:
        p = os.path.join(racine, f)
        print(f'{os.path.getsize(p)/1e6:8.1f} Mo  {p.replace(BASE, "")}')
```

---

## 4. Après la récupération

1. Copier le dossier Drive `Observatoire_QPV/sources/` dans le kit, à la place de `sources/`, sans effacer `sources/action_logement/`, `sources/gares/` ni `sources/communes/communes_coordonnees.csv` (exports Power BI).
2. Dans `scripts/`, lancer `python mettre_a_jour.py`. L'étape 8 (contrôles) s'exécute à la fin.
3. Lire `sortie/controles_fiabilite.txt`. Il ne doit rester aucune ligne « ALERTE » inexpliquée.
4. Mettre à jour les millésimes cités dans les textes (étape E du guide `00_LISEZMOI_MISE_A_JOUR.md`).

## 5. Si un lien ne marche plus

- **Insee :** les numéros de page changent à chaque millésime. Partir du sommaire https://www.insee.fr/fr/statistiques/8186144 (données QPV 2024), repérer la nouvelle page du thème, et remplacer son numéro dans la cellule 2.
- **data.gouv.fr :** l'identifiant du jeu (`5a561801c751df42d7fca9b6`) reste stable, mais les fichiers qu'il contient changent. La cellule 1 les liste toujours.
- **IGN :** si le nom de la couche change (passage à une BD TOPO V4 par exemple), le catalogue est consultable sur `https://data.geopf.fr/wfs/ows?SERVICE=WFS&REQUEST=GetCapabilities`. Chercher `zone_d_activite`.
- **Colonnes renommées :** les scripts du kit s'arrêtent avec `KeyError` et le nom de la colonne. Corriger ce nom en tête du script concerné.
