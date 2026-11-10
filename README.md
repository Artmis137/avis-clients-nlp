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
- Jeu de données : **McDonald's Store Reviews**, fourni par l'enseignante (`McDonald_s_Reviews.csv`, également publié sur Kaggle sous ce nom).
- Contenu : 33 396 avis Google en anglais sur 40 restaurants McDonald's aux États-Unis.
- Variables utiles : texte de l'avis, note (1 à 5 étoiles), adresse du restaurant, latitude et longitude, ancienneté relative de l'avis (« 3 years ago »).
- Étiquettes : 1-2 étoiles = négatif, 3 = neutre, 4-5 = positif.
- Doublons : 11 115 textes exactement identiques (souvent des avis d'un mot : « Excellent », « Good », « Neutral »), supprimés **avant** le découpage ; il reste 22 281 avis (environ 42 % négatifs, 14 % neutres, 44 % positifs).
- Découpage train / validation / test fixé une fois pour toutes (graine 42), stratifié sur le sentiment.
- Points d'attention : environ 3 000 avis contiennent des caractères mal encodés (à traiter au nettoyage) ; les dates sont relatives, donc l'évolution dans le temps reste approximative.
- Les données ne sont pas versionnées : placer le fichier dans `data/raw/`, ou le récupérer avec `scripts/download.py`.

### Pourquoi ce jeu plutôt que les autres
| Fichier | Verdict |
|---|---|
| McDonald's (33 396 avis) | Retenu : taille adaptée à Colab, classes exploitables, thèmes lisibles pour un décideur, restaurants géolocalisés |
| Avis vêtements (23 486 avis) | Écarté : notes très positives (4 % de 1 étoile), classes négative et neutre trop rares, pas de date |
| Amazon (4 915 avis) | Écarté : un seul produit, 80 % de 5 étoiles |
| Tweets de sentiment (4 815) | Écarté : pas des avis clients, pas de note, déjà prétraité |

## Niveau visé
Niveau standard : nettoyage paramétrable, baseline, DistilBERT et BERT, thèmes, tableau de bord avec carte des restaurants. Analyse par aspects (attente, service, propreté, qualité des produits, exactitude de la commande) si le temps le permet.

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
- Branches `feature/<sujet>`, Pull Request relue par l'autre membre avant fusion dans `main`.
- Commits au format Conventional Commits (`feat:`, `fix:`, `docs:`, `test:`, `chore:`).
- Graine aléatoire fixée (42) pour tous les découpages et entraînements.
