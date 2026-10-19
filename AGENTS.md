# AGENTS.md : invariants du projet Printelle

Court par conception. Les détails vivent dans `docs/`. Ce fichier est lu à chaque session d'agent et à chaque arrivée d'une personne sur le projet. **Une règle écrite ici doit avoir un garant** (une vérification automatique ou une ligne de relecture) : sinon elle n'a pas sa place ici.

Références : `GLOSSARY.md` (vocabulaire), `docs/prd/` (exigences), `docs/adr/` (décisions), `docs/README.md` (où est quoi), `CONTRIBUTING.md` (règles de travail).

## Stack

<!-- À REMPLIR: une ligne par composant, avec la décision (ADR) qui l'impose. Exemple :
- Backend : <langage / framework>, API REST, <base de données> (ADR 000X)
- Mobile : <langage / framework>, un seul gestionnaire d'état : <nom> (ADR 000Y)
- Web : <langage / framework>, client de l'API uniquement, aucune règle métier (ADR 000Z)
- Contrat : OpenAPI généré depuis l'API, clients générés, jamais écrits à la main -->

Applications déclarées dans `.kit.conf` (`APPS`) ; chacune a son `CODING.md`.

## Architecture

<!-- À REMPLIR: l'architecture retenue et ses règles de dépendance. Modèle éprouvé (monolithe modulaire en couches) :

| Couche | Contenu | Peut dépendre de |
|---|---|---|
| Domain | entités, objets-valeurs, machine à états, règles, ports (interfaces) | Shared |
| Application | cas d'usage (un par classe) | Domain, Shared |
| Infrastructure | base de données, adapters vendors | Domain, Application, Shared |
| Http | contrôleurs, validation d'entrée, ressources de sortie | Application, Domain (types), Shared |

- Le Domain n'importe ni framework, ni SDK tiers. Vérifié par un outil d'architecture.
- Un module ne touche pas aux classes internes d'un autre : cas d'usage public ou événement de domaine.
- Liste des modules : ... -->

## Invariants métier (non négociables)

<!-- À REMPLIR: 5 à 10 règles numérotées, chacune testable. Un invariant est une règle dont la violation est un incident, pas un bug.
Familles à passer en revue pour tout projet qui manipule des données de personnes :
1. Séparation de deux publics (ex. vue interne / vue publique) : deux modèles, deux ressources, deux groupes de routes, jamais l'un obtenu en filtrant l'autre.
2. Visibilité par statut (rien n'est public avant validation).
3. Machine à états explicite ; transitions soumises à permission.
4. Permissions, pas rôles : `canUser($user, 'action:ressource')`. Les profils sont des conteneurs de permissions.
5. Cloisonnement « besoin d'en connaître » : toute requête sur une ressource est filtrée par périmètre, jamais seulement par utilisateur.
6. Lecture d'une donnée très sensible : motif obligatoire + écriture dans un journal d'audit append-only.
7. Donnée d'identité : chiffrée au repos, jamais affichée en clair, jamais journalisée.
8. Notifications neutres : aucun détail sensible.
9. Localisation ou donnée précise : généralisée pour le public. -->

## Ports à implémenter (adapters remplaçables)

<!-- À REMPLIR: les dépendances externes ou incertaines, chacune derrière une interface (port) avec un adapter remplaçable.
Exemple : Clock, SmsGateway, PushGateway, MediaStorage, PaymentGateway.
Tant que l'ADR correspondant est « en attente », seuls des adapters factices ou manuels existent. -->

## Base de données

<!-- À REMPLIR: conventions de schéma. Modèle éprouvé :
`snake_case` pluriel, clé primaire UUID v7, `created_at` partout, `updated_at` si mutable, horodatages avec fuseau,
`jsonb` (jamais `json`), clé étrangère avec action explicite, tables de jonction avec clé primaire composite.
Une contrainte critique est une contrainte de base de données, pas seulement du code. -->

## Clean code : règles communes à toutes les piles

Les seuils sont contrôlés par la machine (voir la configuration de chaque pile). La relecture couvre le reste. Le détail et les exemples sont dans le `CODING.md` de chaque application.

| Règle | Ce que ça veut dire |
|---|---|
| Pas de type flou | `any`, `mixed`, `dynamic`, `interface{}` interdits hors frontière ; toute entrée est validée et typée tout de suite |
| Types distincts | identifiants et valeurs métier en types dédiés (objet-valeur, type brandé), jamais une chaîne partout |
| États en énumération | `enum` / union fermée et `match` / `switch` **exhaustif**, sans cas par défaut |
| Échec métier attendu | valeur de retour typée (résultat, enum de raison), pas exception ; exception seulement pour l'invariant violé |
| Jamais d'erreur avalée | pas de capture vide ; contexte ajouté puis relance, ou décision explicite écrite |
| Taille | fonction ≤ 40 lignes, complexité ≤ 10, imbrication ≤ 3 (retours anticipés), fichier ≤ 250 à 300 lignes |
| Paramètres | ≤ 3 positionnels ; au-delà, un objet de commande ou des paramètres nommés |
| Booléens | nommés `is`, `has`, `can`, `should` ; jamais muets en paramètre |
| Immutabilité | immuable par défaut ; une transition retourne un nouvel état |
| Dépendances inversées | port (interface) dans le domaine, adapter dans l'infrastructure, branchement dans la composition |
| Pas de verbosité | pas de commentaire qui répète le code, d'abstraction « au cas où », de couche de délégation sans valeur |

## Sécurité par conception

Règles techniques S1 à S12 (routes authentifiées, journaux par liste blanche, limitation de débit, fichiers privés, secrets, dépendances) : `docs/policies/securite.md`. Une règle de ce fichier ou de ce document ne se contourne pas pour passer au vert.

## Écrire du code dans une application

Avant de coder dans une application, lire son `CODING.md` (chemin déclaré dans `.kit.conf`). Il dit où va chaque chose, comment écrire une fonction, les erreurs, les tests et la définition de « terminé ».

## Commandes

```
make hooks    # une fois par clone
make check    # secrets + lint + statique + architecture + tests de chaque application
make ci-local # rejoue tout le job gate puis publie le statut local-ci (porte de fusion quand la CI hébergée est indisponible)
make help     # toutes les commandes
```

Avant de passer une PR « prête » : `make ci-local` vert (arbre propre, commit poussé). Ne jamais publier le statut `local-ci` à la main ni sans avoir lancé le script.

## Revue de code

Chaque constat est vérifié dans le document source qu'il cite (PRD, ADR, design) avant publication. Bloquant : invariant de ce fichier, sécurité, exigence de la spec qui concerne cette PR ; le reste est une suggestion (détail : `CONTRIBUTING.md` §10). Aucune signature ni mention d'outil dans les commits, PR et commentaires.

## Agent skills

### Issue tracker

Les tickets sont des issues GitHub ; un ticket par application (`app:<nom>`), chacun bloqué par le ticket de contrat de sa fonctionnalité. Voir `docs/agents/issue-tracker.md`.

### Triage labels

Les cinq étiquettes par défaut (`needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`) plus `en-attente-decision`, sans mention d'outil dans les commentaires. Voir `docs/agents/triage-labels.md`.

### Domain docs

Un seul contexte : `GLOSSARY.md` et `docs/adr/` à la racine. Voir `docs/agents/domain.md`.

## Avant toute PR

Remplir la checklist de `.github/pull_request_template.md`. Une violation d'un invariant, une requête sans filtre de périmètre, un test sur un nom de rôle ou une vue publique obtenue par filtrage d'une vue interne sont des défauts bloquants.
