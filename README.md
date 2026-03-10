# fil-rouge-passoires-thermiques
# 🔥 Analyse des Passoires Thermiques — Projet Fil Rouge Data Analyst

<div align="center">

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![GeoPandas](https://img.shields.io/badge/GeoPandas-139C5A?style=for-the-badge&logo=python&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)

<br/>

> **Identification des communes prioritaires pour la rénovation énergétique**  
> à partir des données DPE, revenus, population et géographie communale

<br/>

| 👩‍💻 Auteure | 🎓 Encadrant | 🏫 Formation | 📅 Année |
|---|---|---|---|
| Mawada Ennaciri | Yassine Ammami | Simplon — Data Analyst | 2025–2026 |

</div>

---

## 📋 Table des matières

- [Contexte & Problématique](#-contexte--problématique)
- [Architecture du projet](#-architecture-du-projet)
- [Sources de données](#-sources-de-données-option-2-simplon)
- [Installation](#-installation)
- [Utilisation](#-utilisation)
- [Structure du repository](#-structure-du-repository)
- [KPIs & Score composite](#-kpis--score-composite)
- [Résultats & Dashboard](#-résultats--dashboard)
- [Livrables](#-livrables)
- [Outils & Technologies](#-outils--technologies)

---

## 🎯 Contexte & Problématique

La transition énergétique est une **priorité nationale en France**. Les logements classés **F et G au DPE** — appelés *passoires thermiques* — représentent un enjeu majeur :

- 💸 Précarité énergétique des ménages
- 🌍 Émissions élevées de CO₂
- 🏛️ Coûts importants pour les collectivités

> **❓ Question analytique centrale :**  
> *Quelles communes présentent la plus forte concentration de logements énergivores, combinée à une population vulnérable économiquement, et doivent être prioritaires pour les politiques de rénovation énergétique ?*

**Périmètre géographique :** 3 régions françaises
- 🗺️ Île-de-France
- 🗺️ Hauts-de-France  
- 🗺️ Bretagne

---

## 🏗️ Architecture du projet

```
┌─────────────────────────────────────────────────────────────────┐
│                    PIPELINE DATA COMPLET                        │
├─────────────┬──────────────┬─────────────┬──────────────────────┤
│  COLLECTE   │     EDA      │  NETTOYAGE  │   ANALYSE & VISU     │
│             │              │  & FUSION   │                      │
│ API REST    │ Statistiques │ Valeurs     │ Score composite      │
│ CSV/ZIP     │ Distributions│ manquantes  │ Clustering           │
│ SQLite      │ Corrélations │ Jointures   │ Dashboard Power BI   │
│             │ Cartographie │ KPIs        │ Carte choroplèthe    │
└─────────────┴──────────────┴─────────────┴──────────────────────┘
```

---

## 📊 Sources de données — Option 2 Simplon

> ✅ **3 types de sources différentes** conformes au référentiel Simplon

| # | Dataset | Type source | Format | Lien |
|---|---|---|---|---|
| 1 | DPE logements | **API REST** | JSON paginé | [ADEME](https://data.ademe.fr) |
| 2 | Population communale | **API REST** | JSON | [geo.api.gouv.fr](https://geo.api.gouv.fr) |
| 3 | GeoJSON communes | **API REST** | GeoJSON | [geo.api.gouv.fr](https://geo.api.gouv.fr) |
| 4 | Revenus INSEE Filosofi | **Fichier CSV/ZIP** | CSV (latin-1, sep=;) | [INSEE](https://www.insee.fr) |
| 5 | Logements RP2022 | **Base de données SQL** | SQLite | [INSEE RP](https://www.insee.fr) |

### 🔑 Clé de jointure

Tous les datasets sont reliés par le **code INSEE commune** :

| Dataset | Colonne |
|---|---|
| DPE ADEME | `code_insee_commune_actualise` |
| Population | `CODGEO` |
| Revenus Filosofi | `CODGEO` |
| Logements SQLite | `CODGEO` |
| GeoJSON | `code` ou `insee_com` |

---

## ⚙️ Installation

### Prérequis

```bash
Python 3.10+
pip
jupyter
```

### Cloner le repository

```bash
git clone https://github.com/ton-username/passoires-thermiques-fil-rouge.git
cd passoires-thermiques-fil-rouge
```

### Installer les dépendances

```bash
pip install -r requirements.txt
```

**`requirements.txt`**
```
pandas>=2.0.0
numpy>=1.24.0
geopandas>=0.14.0
matplotlib>=3.7.0
seaborn>=0.12.0
requests>=2.31.0
jupyter>=1.0.0
ipykernel>=6.0.0
scipy>=1.10.0
scikit-learn>=1.3.0
folium>=0.14.0
```

---

## 🚀 Utilisation

Exécuter les scripts dans l'ordre suivant :

### Étape 1 — Collecte des données

```bash
python scripts/01_collecte_volume.py
```

> ⏱️ Durée estimée : ~5 minutes  
> 📁 Résultat : dossier `data/` avec 7 fichiers

```
data/
├── dpe/
│   ├── dpe_11.csv          # Île-de-France
│   ├── dpe_32.csv          # Hauts-de-France
│   └── dpe_53.csv          # Bretagne
├── population/
│   └── population_communes.csv
├── revenus/
│   └── revenus_communes.csv
├── logements/
│   ├── logements_communes.csv
│   └── logements_rp2022.db  ← base SQLite
└── geo/
    └── communes_3regions.geojson
```

### Étape 2 — EDA (Analyse Exploratoire)

```bash
jupyter notebook notebooks/02_EDA.ipynb
```

> 📊 Génère 11 graphiques dans `outputs/eda/`

### Étape 3 — Nettoyage & Fusion

```bash
python scripts/03_nettoyage_fusion.py
```

> 🧹 Produit `data/processed/dataset_consolide.csv`

### Étape 4 — Analyse & KPIs

```bash
jupyter notebook notebooks/04_analyse_kpi.ipynb
```

### Étape 5 — Dashboard Power BI

Ouvrir `dashboard/passoires_thermiques.pbix` dans Power BI Desktop.

---

## 📁 Structure du repository

```
passoires-thermiques-fil-rouge/
│
├── 📂 scripts/
│   ├── 01_collecte_volume.py       # Collecte toutes sources
│   ├── 03_nettoyage_fusion.py      # Nettoyage & fusion datasets
│   └── utils/
│       └── helpers.py              # Fonctions utilitaires
│
├── 📂 notebooks/
│   ├── 02_EDA.ipynb                # Analyse exploratoire complète
│   └── 04_analyse_kpi.ipynb        # KPIs & score composite
│
├── 📂 data/
│   ├── dpe/                        # Données DPE par région
│   ├── population/                 # Population communale
│   ├── revenus/                    # Revenus INSEE Filosofi
│   ├── logements/                  # Parc immobilier + SQLite
│   ├── geo/                        # GeoJSON communes
│   └── processed/                  # Données nettoyées & fusionnées
│
├── 📂 outputs/
│   ├── eda/                        # Graphiques EDA (PNG)
│   ├── figures/                    # Visualisations finales
│   └── reports/                    # Rapports exportés
│
├── 📂 dashboard/
│   └── passoires_thermiques.pbix   # Dashboard Power BI
│
├── 📂 docs/
│   ├── cahier_des_charges.docx     # Cahier des charges complet
│   └── rapport_analytique.pdf      # Rapport data storytelling
│
├── 📄 requirements.txt             # Dépendances Python
├── 📄 .gitignore
└── 📄 README.md
```

---

## 📐 KPIs & Score composite

### Indicateurs calculés

| KPI | Formule | Interprétation |
|---|---|---|
| **Taux passoires** | `nb_F_G / nb_total_dpe × 100` | % logements énergivores |
| **Densité passoires** | `nb_passoires / population × 1000` | Passoires pour 1000 hab |
| **Vulnérabilité revenus** | `1 − (revenu_médian / max_revenu)` | Score 0→1 (1 = très pauvre) |

### Score composite de priorité

```
Score_priorité = (taux_passoires_norm)    × 0.40
               + (vulnérabilité_revenus)   × 0.35
               + (densité_passoires_norm)  × 0.25
```

> Chaque indicateur est normalisé entre 0 et 1 (Min-Max)  
> Score final entre 0 (priorité faible) et 1 (priorité maximale)

---

## 📈 Résultats & Dashboard

### Visualisations produites

| # | Graphique | Description |
|---|---|---|
| 01 | Carte des valeurs manquantes | Qualité par dataset |
| 02 | Distribution classes DPE | Répartition A→G par région |
| 03 | Histogrammes surface & conso | Distributions numériques |
| 04 | Revenus médians | Distribution et boxplots dép. |
| 05 | Population communale | Répartition par taille |
| 06 | Passoires par région | Taux F+G et répartition F/G |
| 07 | Type bâtiment & décennie | Passoires selon ancienneté |
| 08 | Revenus & pauvreté | Taux de pauvreté par département |
| 09 | Matrice de corrélation | Liens entre variables |
| 10 | Scatter revenu vs passoires | Corrélation clé du projet |
| 11 | Carte choroplèthe | Visualisation géographique |

### Dashboard Power BI

Le dashboard final comprend :
- 🗺️ Carte interactive des communes prioritaires
- 📊 KPIs dynamiques filtrables par région / département
- 📉 Évolution du score composite
- 🏆 Top communes à rénover en priorité

---

## 📦 Livrables

- [x] 📄 Cahier des charges (`docs/cahier_des_charges.docx`)
- [x] 🐍 Scripts Python collecte & nettoyage (`scripts/`)
- [x] 📓 Notebooks EDA & analyse (`notebooks/`)
- [x] 🗄️ Base de données SQLite (`data/logements/logements_rp2022.db`)
- [x] 📊 Dashboard Power BI (`dashboard/`)
- [ ] 📝 Rapport analytique data storytelling (`docs/rapport_analytique.pdf`)
- [ ] 🎤 Support de présentation (`docs/slides.pptx`)

---

## 🛠️ Outils & Technologies

<div align="center">

| Catégorie | Outils |
|---|---|
| **Langage** | Python 3.10+ |
| **Data** | Pandas, NumPy, GeoPandas |
| **Visualisation** | Matplotlib, Seaborn, Folium |
| **Base de données** | SQLite, SQL |
| **BI & Dashboard** | Power BI Desktop |
| **Géospatial** | GeoPandas, GeoJSON |
| **Sources données** | API REST ADEME, INSEE, geo.api.gouv.fr |
| **Dev** | VS Code, Jupyter Notebook |
| **Gestion projet** | JIRA, Confluence |
| **Versioning** | Git, GitHub |
| **Cloud (optionnel)** | Databricks |

</div>

