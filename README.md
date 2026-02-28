#  Portfolio Risk Management & Quantitative Analysis
### MSFT · TSLA · XOM | 2021–2024


[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![License](https://img.shields.io/badge/License-Academic-green?style=flat-square)]()
[![Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)]()

---

## Table des Matières

1. [Vue d'ensemble](#-vue-densemble)
2. [Objectifs](#-objectifs)
3. [Données & Méthodologie](#-données--méthodologie)
4. [Résultats — Modèle de Marché](#-1-modèle-de-marché-régression-linéaire)
5. [Résultats — Performance du Portefeuille](#-2-performance-du-portefeuille)
6. [Résultats — Gestion des Risques (VaR & CVaR)](#-3-gestion-des-risques-var--cvar)
7. [Résultats — Optimisation de Markowitz](#-4-optimisation-de-markowitz)
8. [Résultats — Simulation Monte Carlo](#-5-simulation-monte-carlo-gbm)
9. [Conclusion](#-conclusion--compétences-acquises)

---

##  Vue d'ensemble

Ce projet présente une **analyse quantitative approfondie** et une **modélisation du risque** appliquées à un portefeuille mixte composé de titres technologiques et énergétiques. L'étude couvre l'intégralité de la chaîne de valeur d'un *Risk Manager* : de la collecte des données jusqu'à la simulation stochastique, en passant par l'optimisation sous contraintes.

**Portefeuille analysé :** Microsoft (MSFT) · Tesla (TSLA) · ExxonMobil (XOM)  
**Période d'étude :** Janvier 2021 – Décembre 2024 (~1 000 jours de bourse)  
**Benchmark :** S&P 500

---

##  Objectifs

- Évaluer la **sensibilité des actifs** par rapport au marché via le coefficient Bêta
- Mesurer l'**efficacité de la diversification** sectorielle (Tech vs. Énergie)
- Quantifier l'**exposition aux pertes extrêmes** via la VaR et la CVaR
- Identifier le **portefeuille tangent** maximisant le ratio de Sharpe (Markowitz)
- Modéliser les **trajectoires futures** du portefeuille par simulation de Monte Carlo (GBM)

---

##  Données & Méthodologie

Les données de cours ajustés ont été collectées via l'API `yfinance`. Les rendements journaliers logarithmiques ont été utilisés pour toutes les analyses statistiques.

| Paramètre | Valeur |
|---|---|
| Source des données | Yahoo Finance (`yfinance`) |
| Type de rendement | Log-rendements journaliers |
| Taux sans risque | 4.0% annuel (approx. T-Bill US) |
| Horizon VaR | 1 jour |
| Niveau de confiance | 95% |
| Simulations Monte Carlo | 5 000 trajectoires |

---

##  1. Modèle de Marché (Régression Linéaire)

Le modèle de Sharpe (CAPM univarié) a été appliqué pour décomposer le risque de chaque actif en **risque systématique** (lié au marché) et **risque spécifique** (idiosyncratique).

$$R_i = \alpha_i + \beta_i \cdot R_{market} + \varepsilon_i$$

### Résultats des Bêtas

| Actif | Bêta (β) | Profil de Risque | Risque Spécifique (R²) |
|-------|----------|------------------|------------------------|
| **MSFT** | 1.23 | Offensif modéré | 39.18% |
| **TSLA** | 1.96 | Très agressif | 70.73% |
| **XOM** | 0.55 | Défensif / Décorrélé | 89.46% |

> **Lecture :** Un Bêta de 1.96 pour Tesla signifie qu'une variation de +1% du S&P 500 est associée en moyenne à une variation de +1.96% de TSLA. Le risque spécifique élevé d'XOM (89.46%) confirme sa faible corrélation avec le secteur technologique, ce qui en fait un actif clé pour la **diversification**.

---

##  2. Performance du Portefeuille

**Allocation initiale (arbitraire) :** 40% MSFT · 30% TSLA · 30% XOM

| Indicateur | Valeur |
|---|---|
| Rentabilité Annuelle Attendue | **19.43%** |
| Volatilité Annuelle | **26.19%** |
| **Ratio de Sharpe** | **0.63** |

> Le ratio de Sharpe de **0.63** indique une rémunération du risque positive mais modérée. La présence de TSLA (β = 1.96) contribue fortement à la volatilité globale du portefeuille, sans compenser suffisamment en termes de rendement ajusté au risque.

---

##  3. Gestion des Risques (VaR & CVaR)

Conformément au cadre des **mesures de risque cohérentes** (Artzner, Delbaen, Eber & Heath, 1999), deux mesures complémentaires ont été calculées.

### Mesures de Risque (Approche Historique — 95%)

| Mesure | Valeur | Interprétation |
|---|---|---|
| **VaR Historique (95%)** | **-2.73%** | Perte journalière maximale dans 95% des scénarios |
| **CVaR / Expected Shortfall** | **-3.70%** | Perte moyenne conditionnelle dans les 5% de cas extrêmes |

> La **CVaR** est supérieure à la VaR en valeur absolue, ce qui est toujours vérifié par construction. Elle fournit une vision plus conservative et plus cohérente du risque de queue (*tail risk*).

### Analyse Dynamique (Backtesting)

Une VaR **recalculée dynamiquement** (fenêtre glissante) a été appliquée sur la période complète :

| Résultat | Valeur | Théorie (5%) |
|---|---|---|
| Nombre de violations observées | **42** | ~50 violations attendues |
| Taux de violation empirique | **~4.2%** | 5.0% |

> Les **42 dépassements** sur ~1 000 jours de trading confirment la **fiabilité statistique** du modèle. Le taux empirique (4.2%) est proche mais légèrement inférieur au niveau théorique (5%), indiquant un modèle légèrement conservateur — ce qui est souhaitable en gestion des risques.

---

##  4. Optimisation de Markowitz

En appliquant la **théorie moderne du portefeuille** (Markowitz, 1952) et le **théorème de séparation à deux fonds**, l'algorithme d'optimisation sous contraintes a cherché le **portefeuille tangent** qui maximise le ratio de Sharpe.

$$\max_{w} \frac{E[R_p] - R_f}{\sigma_p} \quad \text{s.t.} \sum_i w_i = 1, \; w_i \geq 0$$

### Portefeuille Tangent — Résultats

| Actif | Allocation Optimale | Allocation Initiale |
|-------|---------------------|---------------------|
| **MSFT** | 39.48% | 40% |
| **XOM** | 60.52% | 30% |
| **TSLA** | **0.00%** | 30% |

### Comparaison des Performances

| Indicateur | Portefeuille Initial | Portefeuille Optimal | Variation |
|---|---|---|---|
| Rentabilité Annuelle | 19.43% | **23.71%** | +4.28 pp |
| Volatilité Annuelle | 26.19% | **20.33%** | -5.86 pp |
| **Ratio de Sharpe** | 0.63 | **1.07** | **+70%**  |

> **Résultat clé :** L'algorithme a **exclu Tesla** du portefeuille optimal. Malgré son rendement élevé, la volatilité de TSLA est disproportionnée par rapport à sa contribution marginale au ratio rendement/risque. En surpondérant **XOM (décorrélé du secteur Tech)**, l'optimiseur a réduit la volatilité de ~6 points tout en augmentant le rendement espéré — illustration parfaite du **gain de diversification**.

---

##  5. Simulation Monte Carlo (GBM)

Pour tester la robustesse du portefeuille optimisé sur un horizon futur, **5 000 trajectoires** ont été simulées sur 1 an via le **Mouvement Brownien Géométrique (GBM)**.

$$S_{t+dt} = S_t \cdot \exp\left[\left(\mu - \frac{1}{2}\sigma^2\right)dt + \sigma \sqrt{dt} \cdot Z\right], \quad Z \sim \mathcal{N}(0,1)$$

> La correction de Jensen $-\frac{1}{2}\sigma^2$ est appliquée pour obtenir un estimateur sans biais du drift.

### Paramètres & Résultats

| Paramètre | Valeur |
|---|---|
| Capital Initial | 100 000 $ |
| Horizon | 1 an (252 jours de bourse) |
| Nombre de simulations | 5 000 |
| **Valeur Finale Médiane Attendue** | **~123 700 $** |
| VaR Monte Carlo (95%) | Quantile inférieur à 5% des 5 000 trajectoires |

> La simulation confirme le comportement du portefeuille optimisé et permet de quantifier l'exposition au risque sur un horizon **annuel** en dollars, complémentant ainsi la VaR journalière.

---

##  Conclusion 
Ce projet illustre empiriquement que la diversification et l'optimisation quantitative permettent d'améliorer significativement le profil rendement-risque d'un portefeuille. L'exclusion de Tesla par l'algorithme de Markowitz et le gain de +70% sur le ratio de Sharpe confirment que la rigueur mathématique est un outil indispensable à toute décision d'investissement rationnelle.


---

##  Stack Technique

```
Python 3.10+
├── yfinance          # Collecte des données de marché
├── pandas            # Manipulation et analyse des données
├── numpy             # Calcul matriciel et simulations
├── scipy             # Optimisation et tests statistiques
├── matplotlib        # Visualisations
└── seaborn           # Graphiques statistiques avancés
```

---

##  Installation & Usage

```bash
# 1. Cloner le dépôt
git clone https://github.com/votre-username/Portfolio-Risk-Management.git
cd Portfolio-Risk-Management

# 2. Créer un environnement virtuel (recommandé)
python -m venv venv
source venv/bin/activate  # Linux/macOS
venv\Scripts\activate     # Windows

# 3. Installer les dépendances
pip install -r requirements.txt

# 4. Lancer l'analyse complète
python scripts/main.py
```

