# INRAE — Matrice Justification Contrat — Widgets Grist

Widgets HTML pour la matrice de justification contrat INRAE.
À déployer sur GitHub Pages et à intégrer dans Grist.

---

## Architecture des tables

### Principe fondamental

| Type | Tables | Accès par les widgets |
|---|---|---|
| **Tables d'extraction** | `JDP_2025`, `JDP_2026`, `MISSIONS_2025`, `MISSIONS_2026`, `PI`, `CDA`, `SF`, `FACTURES`, `RAPPRO` | **Lecture seule** — les widgets ne modifient jamais ces tables |
| **Tables auxiliaires** | `PIECES_JUSTIF`, `CONFIG` | Lecture et écriture — créées automatiquement par les widgets si absentes |

Les tables d'extraction sont des exports SIFAC/Notilus importés dans Grist.
Leur structure est figée. Les widgets n'y ajoutent, ne modifient ni ne suppriment aucune ligne.

Chaque utilisateur travaille dans son propre espace Grist — il n'y a pas de partage de document entre utilisateurs, donc pas de colonne `user_email` nécessaire.

### Structure des tables auxiliaires

#### Table `CONFIG` (créée automatiquement)
| Colonne | Type | Rôle |
|---|---|---|
| `etape` | Texte | Identifiant de l'étape (`lancement`, `jdp2025`, `jdp2026`…) |
| `statut` | Texte | `valide`, `en_cours`, `ignore`, `a_recharger` |
| `updated_at` | Texte | Horodatage ISO |

#### Table `PIECES_JUSTIF` (créée automatiquement, vidée au lancement)
Contient les justifications précédentes chargées par l'utilisateur.
Les colonnes métier sont ajoutées lors des imports suivants.

---

## Structure des fichiers

| Fichier | Widget Grist | Étape |
|---|---|---|
| `widget_lancement.html` | Lancement de la matrice | 1 |
| `widget_jdp2025.html` | Validation JDP 2025 | 2 |
| `widget_jdp2026.html` | Validation JDP 2026 | 3 |
| `widget_missions.html` | Import Missions | 4 *(à venir)* |
| `widget_pi.html` | Import Prestations internes | 5 *(à venir)* |
| `widget_cda.html` | Import Commandes d'achat | 6 *(à venir)* |
| `widget_sf.html` | Import Services faits | 7 *(à venir)* |
| `widget_factures.html` | Import Factures | 8 *(à venir)* |
| `widget_rappro.html` | Import Rapprochement | 9 *(à venir)* |
| `widget_export.html` | Export vers SIFAC | 10 *(à venir)* |
| `widget_nettoyage.html` | Nettoyage des tables | 11 *(à venir)* |

---

## Déploiement sur GitHub Pages

### 1. Créer le dépôt GitHub

1. Connectez-vous à [github.com](https://github.com)
2. Cliquez sur **New repository**
3. Nommez-le `inrae-matrice-widgets`
4. Laissez en **Public** (requis pour GitHub Pages gratuit)
5. Cliquez **Create repository**

### 2. Déposer les fichiers

**Via l'interface web (sans Git) :**
1. Dans le dépôt, cliquez **Add file → Upload files**
2. Glissez tous les fichiers `.html`
3. Cliquez **Commit changes**

**Via Git :**
```bash
git clone https://github.com/VOTRE-COMPTE/inrae-matrice-widgets.git
# Copiez les fichiers HTML dans le dossier cloné
git add .
git commit -m "Ajout des widgets Grist"
git push
```

### 3. Activer GitHub Pages

1. Dans le dépôt, allez dans **Settings → Pages**
2. Sous **Source** : **Deploy from a branch** → branche **main** → dossier **/ (root)**
3. Cliquez **Save**

Vos widgets sont ensuite accessibles à :
```
https://VOTRE-COMPTE.github.io/inrae-matrice-widgets/widget_lancement.html
https://VOTRE-COMPTE.github.io/inrae-matrice-widgets/widget_jdp2025.html
https://VOTRE-COMPTE.github.io/inrae-matrice-widgets/widget_jdp2026.html
```

---

## Intégration dans Grist

### Ajouter les widgets sur une page

1. Ouvrez votre document Grist
2. Créez une nouvelle page (icône **+** dans la barre latérale)
3. Ajoutez une vue → **Widget personnalisé**
4. Dans le panneau de droite, collez l'URL du widget
5. Accordez l'accès **Lecture et écriture** quand Grist le demande
6. Répétez pour chaque widget en les empilant sur la même page

### Ordre des widgets sur la page

Empiler de haut en bas :
1. `widget_lancement.html`
2. `widget_jdp2025.html`
3. `widget_jdp2026.html`
4. *(suite à venir)*

---

## Enchaînement entre widgets

Chaque widget vérifie au démarrage que l'étape précédente est marquée
`valide` dans la table `CONFIG`. Si ce n'est pas le cas, le widget
affiche un message bloquant indiquant quelle étape compléter d'abord.

| Widget | Vérifie dans CONFIG | Écrit dans CONFIG |
|---|---|---|
| `widget_lancement` | *(aucune vérification)* | `lancement = valide` |
| `widget_jdp2025` | `lancement = valide` | `jdp2025 = valide` |
| `widget_jdp2026` | `jdp2025 = valide` | `jdp2026 = valide` |
| *(suite)* | `jdp2026 = valide` | … |

---

## Support

En cas d'erreur, suivre les instructions affichées dans le widget :
déposer un ticket Ariane via SIFAC (Espace Aide → Créer un ticket Ariane).
