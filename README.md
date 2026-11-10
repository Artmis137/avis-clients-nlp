# Analyse intelligente des avis clients (NLP)

Projet de la matière "Analyse et restitution", M1 MLSD, Université Paris Cité (2026-2027).
Binôme : Mamadou NIMAGA et Yhan HOUNTADA. Encadrante : Ikram Chourib. Soutenance le 15/03/2027.

## Problématique
Une entreprise reçoit des milliers d'avis textuels que personne ne lit en entier. Comment transformer ce volume en informations décisionnelles fiables (satisfaction, thèmes, points de friction), et que gagne-t-on réellement avec un modèle de type BERT par rapport à une baseline classique ?

## Questions auxquelles le projet répond
1. Quel est l'effet de chaque étape de nettoyage, sur la baseline et sur le Transformer ?
2. Baseline TF-IDF contre DistilBERT contre BERT : quel compromis entre F1 macro, temps d'inférence et mémoire ?
3. Pourquoi la classe neutre et les avis difficiles (ironie, avis mixtes, note qui contredit le texte) posent-ils problème ?
4. Quels sont les thèmes dominants et les points de friction, validés par une évaluation humaine ?

## Données
- Jeu de données : à choisir dans le dossier fourni par l'enseignante (une seule catégorie).
- Étiquettes : 1-2 étoiles = négatif, 3 = neutre, 4-5 = positif.
- Doublons supprimés avant le découpage ; découpage train / validation / test fixé une fois pour toutes (graine 42).
- Sous-échantillonnage à environ 20 000 avis d'entraînement si nécessaire (Colab).
- Les données ne sont pas versionnées : elles sont récupérées par `scripts/download.py`.

## Niveau visé
Niveau standard : nettoyage paramétrable, baseline, DistilBERT et BERT, thèmes, tableau de bord. Analyse par aspects (prix, livraison, qualité, service) si le temps le permet.

## Répartition des rôles
- Mamadou : Transformers (DistilBERT, BERT/CamemBERT), mesures de temps et de mémoire, tableau de bord Streamlit, analyse des avis difficiles.
- Yhan : exploration, chaîne de nettoyage paramétrable, baseline (TF-IDF + LR / SVM / Naive Bayes), LDA et mots-clés.
- Ensemble : BERTopic, annotation humaine de 100 avis chacun (kappa de Cohen), rapport, note pour le décideur, présentation.

Chacun relit le travail de l'autre (Pull Request) et les deux savent expliquer l'ensemble du projet.

## Planning
| Étape | Date | Attendu |
|---|---|---|
| Séance 1 | 10/11/2026 | Dépôt et README de cadrage, données téléchargées, exploration lancée |
| Travail autonome | Nov. - déc. | Exploration, nettoyage paramétrable, baseline, premier DistilBERT |
| Séance 2 | 05/01/2027 | Résultats baseline vs DistilBERT, premier tableau sur l'effet du nettoyage |
| Travail autonome | Janv. - févr. | BERT/CamemBERT, thèmes, annotation humaine, tableau de bord, rapport v1 |
| Séance 3 | 01/03/2027 | Revue des résultats, test de la démo, plan de la présentation |
| Dépôt final | 12/03/2027 | Tag v1.0, rapport, présentation, vidéo de secours |
| Soutenance | 15/03/2027 | 5 à 10 minutes + questions |

## Structure du dépôt
## Structure du dépôt
```
avis-clients-nlp/
├── README.md
├── CONTRIBUTIONS.md        # qui a fait quoi
├── pyproject.toml          # dépendances (uv)
├── uv.lock                 # versions verrouillées
├── .python-version         # Python 3.12
├── requirements.txt        # export pip (uv export)
├── .gitignore
├── configs/                # niveaux de nettoyage (YAML)
├── data/                   # vide, rempli par scripts/download.py
├── scripts/                # téléchargement, dédoublonnage, découpage train/val/test
├── src/
│   ├── cleaning/           # une fonction par étape de nettoyage
│   ├── models/             # baseline, DistilBERT, BERT
│   ├── topics/             # LDA, BERTopic, clustering
│   └── metrics/            # F1 macro, temps, mémoire, kappa
├── notebooks/              # exploration et figures
├── results/                # tableaux CSV et figures
├── app/                    # tableau de bord Streamlit
├── docs/                   # rapport, présentation, journal de bord
└── tests/                  # tests unitaires
```

## Installation
Le projet utilise [uv](https://docs.astral.sh/uv/) (Python 3.12).

```bash
git clone https://github.com/Artmis137/avis-clients-nlp # Installer git si c'est pas déjà fait
cd avis-clients-nlp
uv sync                          # installe l'environnement
uv run python scripts/download.py
uv run pytest                    # lance les tests
```

Sans uv : `pip install -r requirements.txt` (généré par `uv export`).

## Conventions
- Chaque tâche a un ticket Jira (clé `AVIS-xx`), suivi sur le tableau Kanban : À faire → En cours → En revue → Terminé.
- Branches : `feature/AVIS-xx-description-courte` (ex. `feature/AVIS-15-download-script`) ; `fix/AVIS-xx-...` pour une correction.
- Commits au format Conventional Commits, avec la clé du ticket : `feat(data): add download script (AVIS-15)`.
- Pull Request : titre commençant par la clé (`AVIS-15 Script de téléchargement`), relue et validée par l'autre membre avant fusion dans `main`.
- Le ticket passe en « En revue » à l'ouverture de la PR, puis en « Terminé » à la fusion.
- Graine aléatoire fixée (42) pour tous les découpages et entraînements.