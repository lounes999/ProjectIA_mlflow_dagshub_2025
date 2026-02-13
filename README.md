# MediaNova — Classification automatique d’articles (BBC)  
**Partie 1 : Choix du modèle (NLP/ML)** • **Partie 2 : Déploiement (FastAPI • Docker • Cloud Run)**

## Introduction (contexte)
### a) Client fictif
**MediaNova** est un groupe média numérique fictif qui publie chaque jour des centaines d’articles et newsletters. L’entreprise souhaite améliorer l’organisation et la découvrabilité de ses contenus (site, app, infolettres). Elle veut un système qui classe automatiquement les articles par thème pour accélérer la mise en ligne, faciliter la recherche interne et personnaliser les recommandations.

### b) Problématique métier
Comment automatiser l’assignation d’un thème à chaque article (ex. *sport, business, tech, entertainment, politics*) pour :
- accélérer le flux éditorial (moins d’annotations manuelles),
- améliorer la navigation et la recherche,
- préparer la personnalisation des contenus.

---

## À propos du projet
Projet réalisé dans le cadre du cours **Projet IA : Méthodologie et déploiement**.  
**Auteurs :** Lounes Behlouli **  
**Période :** 1 mois  
**Tâche :** classifier des articles du dataset **BBC-text.csv** en 5 catégories :  
`business`, `politics`, `sport`, `tech`, `entertainment`.

---

## Partie 1 — Choix du modèle (Modélisation)

### Données
- **Dataset :** `BBC-text.csv`
- **Taille :** 2225 articles
- **Cible :** `category`
- **Texte :** `text`
- **Qualité :**
  - pas de valeurs manquantes notables
  - doublons détectés puis supprimés

### Prétraitement NLP
- mise en minuscules
- suppression : liens, chiffres, ponctuation, caractères spéciaux
- suppression des doublons
- vectorisation **TF‑IDF**
  - `stop_words='english'`
  - tests sur `ngram_range`: (1,1) et (1,2)
  - tests sur `max_df`: 0.9 et 1.0

### Modèles comparés (baseline)
Trois modèles supervisés ont été testés :
- **Multinomial Naive Bayes**
- **LinearSVC (SVM linéaire)**
- **Régression Logistique**

Résultat : les modèles obtiennent d’excellentes performances (> 97%). Les meilleurs candidats étaient **LinearSVC** et **Régression Logistique**.

### Optimisation (GridSearchCV)
Mise en place d’une recherche par grille avec validation croisée **3-fold** sur le pipeline :

**TF‑IDF → Modèle (LinearSVC / Logistic Regression)**

Paramètres explorés :
- `tfidf__ngram_range` : (1,1), (1,2)
- `tfidf__max_df` : 0.9, 1.0
- `model__C` : 0.5, 1, 2

Meilleures combinaisons :
- **Best LinearSVC** : `C=0.5`, `ngram_range=(1,2)`, `max_df=0.9`
- **Best Logistic Regression** : `C=2`, `ngram_range=(1,2)`, `max_df=0.9`

### Résultats (test)
- **Best SVC** : Accuracy ≈ **0.9798**, F1 ≈ **0.9797**
- **Best Logistic Regression** : Accuracy ≈ **0.9865**, F1 ≈ **0.9865**

### Modèle final retenu
**Régression Logistique optimisée** (modèle final)

Justification :
- **Performance** : meilleurs scores globaux
- **Stabilité** : résultats équilibrés sur les catégories
- **Interprétabilité** : modèle linéaire (mots représentatifs par classe)
- **Efficacité** : entraînement rapide et bonne généralisation

---

## Partie 2 — Déploiement (API FastAPI + Docker + Cloud Run)

### Objectif
Déployer le modèle entraîné sous forme de **service web** accessible via une **URL publique**, en passant par :
1) API FastAPI (local)  
2) Conteneur Docker  
3) Déploiement sur **Google Cloud Run**

### API (FastAPI)
- `GET /health` → retourne un statut (ex. `{"status": "API OK"}`)
- `POST /predict` → retourne la catégorie prédite à partir d’un texte

Exemple de requête :
```json  
{"text": "sports news today"}  
