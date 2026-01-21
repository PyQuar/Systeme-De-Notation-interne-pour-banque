# 🏦 Credit Scoring - Système de Notation Interne (SNI)

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Basel II/III](https://img.shields.io/badge/Compliant-Basel%20II%2FIII-red.svg)](https://www.bis.org/bcbs/)

## 📋 Description

Ce projet implémente un **Système de Notation Interne (SNI)** complet pour l'évaluation du risque de crédit bancaire, conforme aux exigences réglementaires **Bâle II/III**. Le modèle prédit la probabilité de défaut de paiement des clients (DPD > 90 jours) et génère une scorecard utilisable en production.

## 🎯 Objectifs

- **Identifier** les clients à risque de défaut de paiement
- **Construire** un modèle de scoring prédictif et interprétable
- **Produire** une scorecard conforme aux standards bancaires
- **Classifier** les clients en classes de risque (Très faible → Très élevé)

## 📊 Performance du Modèle

| Métrique | Train | Test |
|:---------|:-----:|:----:|
| **AUC-ROC** | 0.88 | 0.84 |
| **Gini** | 0.76 | 0.68 |
| **Accuracy** | 85% | 82% |

## 🏗️ Architecture du Projet

```
scoring/
│
├── test.ipynb                          # Notebook principal du scoring
├── base_SNI.xlsx                       # Données source (5 752 clients)
├── bases_train_test.xlsx               # Bases Train/Test exportées
├── resultats_selection_variables.xlsx  # Résultats IV & corrélations
├── resultats_modele_scoring.xlsx       # Scorecard & résultats finaux
├── README.md                           # Documentation
│
├── Presentations/
│   └── Presentation_Etape1_*.pptx      # Présentations PowerPoint
│
└── .venv/                              # Environnement virtuel Python
```

## 🔄 Méthodologie

Le projet suit une approche structurée en **4 étapes** :

### Étape 1 : Data Quality Check & EDA
- Analyse exploratoire des données
- Détection des valeurs manquantes et aberrantes
- Création de la variable cible `flag_defaut` (DPD > 90 jours)
- Statistiques descriptives et visualisations

### Étape 2 : Traitement des Données
- Suppression des identifiants et variables à forte cardinalité
- Imputation des valeurs manquantes (médiane/mode)
- Feature Engineering (ratios, transformations log)
- Encodage **WoE** (Weight of Evidence) des variables catégorielles
- **SMOTE** pour le rééquilibrage des classes (2% → 50%)

### Étape 3 : Sélection des Variables
- Calcul de l'**Information Value (IV)** pour chaque variable
- Sélection des variables avec IV ≥ 0.02
- Analyse de la multicolinéarité (corrélation > 0.7)
- Résultat : **25 variables** retenues sur 33 analysées

### Étape 4 : Modélisation & Scorecard
- **Régression Logistique** (statsmodels + scikit-learn)
- Validation croisée stratifiée (5-fold)
- Optimisation du seuil (Youden's J, F1-Score, Coût métier)
- Génération de la **scorecard** avec classes de risque

## 📈 Classes de Risque

| Classe | Score | Taux de Défaut |
|:-------|:-----:|:--------------:|
| 🔴 Très haut risque | < 400 | > 15% |
| 🟠 Haut risque | 400 - 450 | 8-15% |
| 🟡 Risque moyen | 450 - 525 | 3-8% |
| 🟢 Faible risque | 525 - 600 | 1-3% |
| 🔵 Très faible risque | > 600 | < 1% |

## 🛠️ Technologies Utilisées

- **Python 3.8+**
- **pandas** - Manipulation des données
- **numpy** - Calculs numériques
- **matplotlib / seaborn** - Visualisations
- **scikit-learn** - Machine Learning
- **statsmodels** - Régression logistique statistique
- **imbalanced-learn** - SMOTE pour le rééquilibrage
- **openpyxl** - Export Excel

## 🚀 Installation

### 1. Cloner le repository

```bash
git clone https://github.com/PyQuar/opendoors.git
cd opendoors
```

### 2. Créer l'environnement virtuel

```bash
python -m venv .venv
.venv\Scripts\activate  # Windows
# ou
source .venv/bin/activate  # Linux/Mac
```

### 3. Installer les dépendances

```bash
pip install pandas numpy matplotlib seaborn scikit-learn statsmodels imbalanced-learn openpyxl
```

### 4. Lancer le notebook

```bash
jupyter notebook test.ipynb
```

## 📁 Données

### Source
- **Fichier** : `base_SNI.xlsx` (Feuille 2)
- **Observations** : 5 752 clients bancaires
- **Variables** : 29 caractéristiques initiales

### Variables Clés

| Variable | Description |
|:---------|:------------|
| `CRD` | Capital Restant Dû |
| `DPD 12M` | Jours de retard (12 derniers mois) |
| `Revenus_mensuels` | Revenus mensuels déclarés |
| `Solde_moyen` | Solde moyen du compte |
| `Age_relation` | Ancienneté de la relation bancaire |
| `DTI_ratio` | Ratio d'endettement (Debt-to-Income) |
| `flag_defaut` | Variable cible (1 si DPD > 90) |

## 📊 Résultats & Exports

Le notebook génère plusieurs fichiers de sortie :

| Fichier | Contenu |
|:--------|:--------|
| `bases_train_test.xlsx` | Données Train/Test avec encodage WoE |
| `resultats_selection_variables.xlsx` | IV, corrélations, variables sélectionnées |
| `resultats_modele_scoring.xlsx` | Scorecard, coefficients, résultats test |

## 🔍 Formules Clés

### Information Value (IV)

$$IV = \sum_{i=1}^{n} (Dist_{good,i} - Dist_{bad,i}) \times WoE_i$$

### Weight of Evidence (WoE)

$$WoE = \ln\left(\frac{Dist_{good}}{Dist_{bad}}\right)$$

### Score de la Scorecard

$$Score = Offset + Factor \times \left(\beta_0 + \sum_{i=1}^{n} \beta_i \times X_i\right)$$

## 📝 Conformité Réglementaire

Ce modèle est développé en conformité avec :

- **Bâle II** - Approche IRB (Internal Ratings-Based)
- **Bâle III** - Exigences en fonds propres
- **Définition du défaut** : DPD > 90 jours (standard EBA)

## 👥 Auteur

**PyQuar** - [GitHub](https://github.com/PyQuar)

## 📄 Licence

Ce projet est sous licence MIT - voir le fichier [LICENSE](LICENSE) pour plus de détails.

---

<p align="center">
  <i>Développé avec ❤️ pour l'analyse du risque de crédit</i>
</p>
