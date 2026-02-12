README – Déploiement de l’API IA 

Auteurs : Lounes Behlouli

Cours : Projet IA – Méthodologie et Déploiement (UA3)

Date : Novembre 2025

1. Introduction générale

Dans ce projet, nous avons travaillé ensemble pour développer une API d’intelligence artificielle avec
FastAPI,
l’intégrer dans un conteneur Docker, puis la déployer publiquement sur Google Cloud Run. Notre
objectif principal
était de passer d’un modèle IA entraîné dans un notebook local à un service en ligne accessible via
une URL publique.
Ce document explique toutes les étapes suivies, de manière claire et détaillée, comme si nous l’avons
rédigé nous-mêmes.
2. Préparation de l’API en local

Avant toute forme de déploiement, nous avons d’abord vérifié que notre API fonctionne correctement
localement.

2.1 Installation de l’environnement
Nous avons commencé par installer toutes les dépendances du projet à l’aide du fichier
requirements.txt. Ce fichier
contient FastAPI, Uvicorn, scikit-learn, numpy et d’autres librairies essentielles.
Commande utilisée :
pip install -r requirements.txt

2.2 Lancement local de l’API
Pour vérifier que l’API fonctionne sans Docker, nous avons utilisé la commande suivante :
uvicorn app:app --reload
Grâce à --reload, toutes les modifications apportées au code se rechargent automatiquement.

2.3 Vérifications locales

Une fois l’API démarrée :
- L’URL http://127.0.0.1:8000/health devait afficher un statut simple comme {"status": "API OK"}.
- La documentation Swagger http://127.0.0.1:8000/docs nous a permis de tester facilement l’endpoint
/predict.
- Nous avons envoyé un JSON comme {"text": "bonjour"} et avons reçu la prédiction du modèle IA.

3. Dockerisation du projet

Après avoir validé que tout fonctionne localement, nous avons conteneurisé l’API.
3.1 Construction de l’image Docker

La commande suivante construit l’image basée sur le Dockerfile :
docker build -t projet-ia .
3.2 Exécution du conteneur Docker

Une fois l’image créée :
docker run -p 8000:8000 projet-ia
Cela permet d’accéder à l’API à :
http://localhost:8000
3.3 Vérification du conteneur

Pour confirmer que l’image tourne dans Docker, nous avons utilisé :
docker ps

4. Explication des fichiers clés

4.1 Le Dockerfile

Ce fichier contient toutes les instructions pour créer l’image de l’API :
- Installation de Python
- Copie de requirements.txt
- Installation des dépendances
- Copie du code de l’API
- Démarrage automatique d’Uvicorn
4.2 Le fichier requirements.txt

Il liste les versions exactes des librairies nécessaires à la bonne exécution de l’API. Grâce à ce fichier,
Docker
installe les bonnes versions et évite les erreurs liées au chargement du modèle (model.pkl).
5. Structure complète du projet

La structure de notre projet se présente ainsi :
projet_ia/
app.py ® Fichier principal de l’API FastAPI
model.pkl ® Modèle IA entraîné
requirements.txt ® Dépendances Python
Dockerfile ® Instructions Docker
.dockerignore ® Fichiers ignorés durant le build
journal_de_projet.md ® Journal technique du projet
README.pdf ® Documentation du projet
6. Déploiement sur Google Cloud Run

Une fois l’API prête dans Docker, nous avons déployé notre conteneur sur Google Cloud Run.
Étapes principales :
- Connexion à Google Cloud via gcloud
- Build de notre image et envoi vers Container Registry
- Déploiement du conteneur sur Cloud Run avec autorisation publique
L’URL générée par Google Cloud Run est :
https://mon-app-328785983070.us-central1.run.app
7. Test en ligne avec Postman

Nous avons utilisé Postman pour tester la version en ligne de l’API. Nous avons envoyé un JSON
contenant une phrase
à analyser, et l’API nous a renvoyé la catégorie correspondante.
Exemple :
{"text": "sports news today"}
Cloud Run a répondu correctement, confirmant que le modèle fonctionne en production.
8. Erreurs rencontrées et solutions appliquées

- Problème de version scikit-learn : le modèle ne se chargeait pas. Solution : fixer la version correcte.
- model.pkl introuvable : ajustement de la COPY dans le Dockerfile.
- Format JSON incorrect dans Postman : correction de l’attribut "text".
- Autorisations Cloud Run bloquées : activation de l’accès public.
9. Conclusion

  Ce travail nous a permis de comprendre toute la chaîne de déploiement d’un projet IA : préparation du
modèle,
développement d’une API, conteneurisation Docker et déploiement sur Google Cloud Run. Nous avons
appris à créer
des images Docker fiables, à utiliser Cloud Run pour rendre une API disponible publiquement, et à
tester une
solution IA comme dans un vrai contexte professionnel
