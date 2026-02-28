# Classification Multi-Label des Pathologies Thoraciques sur CheXpert (Small)

## 📋 Résumé Exécutif

Ce projet implémente un modèle de **classification multi-label** pour la détection automatique de pathologies thoraciques à partir d'images de radiographies pulmonaires (Chest X-Ray). L'approche se concentre sur la gestion des **dépendances hiérarchiques** entre les maladies et l'intégration des **étiquettes d'incertitude** pour améliorer la précision du diagnostic.

## 📊 Jeu de Données
Le modèle utilise le dataset CheXpert (version small), un sous-ensemble d'environ 11 Go disponible sur Kaggle :
- **Pathologies** : 14 observations initiales sont considérées (ex: Atélectasie, Cardiomégalie, Consolidation, Œdème, Pneumonie, etc.).
- **Modalité** : Radiographies en projection frontale.
- **Format** : Problème multi-étiquette où chaque image peut contenir plusieurs pathologies simultanément.

## 🧠 Stratégie d'Entraînement
Le processus d'apprentissage adopte une stratégie en deux étapes :
1. **Étape 1** – Apprentissage global : Entraînement pendant 2 époques sur l'ensemble des 14 pathologies disponibles pour apprendre des représentations générales des anomalies.
2. **Étape 2** – Fine-tuning ciblé : Affinement du modèle (3e époque) sur 5 pathologies prioritaires (Atélectasie, Cardiomégalie, Consolidation, Œdème, Épanchement pleural) sélectionnées pour leur intérêt clinique et leur prévalence.

## 🛠️ Configuration Technique
- **Architecture** : Réseau **DenseNet-121** pré-entraîné sur ImageNet.
- **Gestion des incertitudes** : Application de la stratégie U-Ones, transformant les étiquettes incertaines (notées "-1") en "1" (positives) pour réduire les faux négatifs en contexte de dépistage.
- **Transformations & Augmentation** :
Redimensionnement à 224 × 224 pixels.
Retournements horizontaux aléatoires (p=0.5) et rotations (±10°).
Normalisation basée sur les statistiques d'ImageNet.
- **Optimisation** : Utilisation de l'optimiseur **Adam** avec une perte de type Binary Cross-Entropy (BCE).

## 📈 Résultats de Validation (AUC)
Les performances ont été mesurées par le score AUC (Area Under the Curve) sur le jeu de validation pour les 5 pathologies cibles :

| Méthode                | Cardiomegaly (AUC) | Edema (AUC) | Consolidation (AUC) | Atelectasis (AUC) | Pleural Effusion (AUC) | Moyenne |
|------------------------|--------------------|-------------|---------------------|-------------------|------------------------|---------|
| U-Ones5          | 0.74               | 0.92        | 0.89                | 0.83              | 0.91                   | 0.858    |
| U-Ones14       | 0.74               | **0.93**        | 0.89                | 0.81              | 0.91                   | 0.856    |
| Pham et al          | 0.45               | 0.57        | 0.62                | 0.64              | 0.89                   | 0.640    |
| Notre méthode       | **0.79**               | 0.92        | **0.92**                | 0.82              | 0.91                   | **0.868**    |

Cette approche ("Notre méthode") surpasse les stratégies standards comme U-Ones14 (0.856) et les résultats rapportés par Pham et al. (2021) sur ce même type de données (0.640).

## 📚 Références
- Irvin et al., CheXpert: A Large Chest Radiograph Dataset with Uncertainty Labels and Expert Comparison, 2019.
- Pham et al., Interpreting chest X-rays via CNNs that exploit hierarchical disease dependencies and uncertainty labels, 2021.