# apps/web : écrire le code (TypeScript / Next.js)

Règles d'écriture du code TypeScript d'une application web Next.js (App Router). Elles complètent, sans les répéter, `AGENTS.md`, `docs/guides/principes-de-conception.md` et l'ADR de l'application. **[machine]** casse `make check` (une fois l'ESLint décrit ci-dessous en place) ; **[relecture]** engage le relecteur. Tant que l'application n'est pas initialisée, tout est **[relecture]**.

Mot-guide : **client**. Si l'application est cliente d'une API (console d'administration, front), elle affiche et déclenche ; elle ne décide de rien. Une règle qui semble manquer ici (permission, cloisonnement, audit) manque dans l'API : on la corrige là-bas.

> Pack **éprouvé en conception** (projet de référence, Next.js, pnpm), **non encore exercé en production** : les règles ESLint de frontières et l'orthographe de `minimumReleaseAge` sont à vérifier à l'initialisation.

## 1. Où va quoi

| Je code… | Dossier | Contrainte |
|---|---|---|
| Une page, un layout | `src/app/` | assemble des `features` ; pas de logique d'écran |
| La logique d'un écran (requêtes, état, formulaires) | `src/features/<feature>/` | importe `components`, `lib`, `api-client` ; jamais une autre feature |
| Un composant d'interface générique | `src/components/ui/` | n'importe rien d'autre du projet |
| Un appel à l'API | via `src/api-client/` | **généré** depuis le contrat, jamais édité ; pas de `fetch(` hors `lib/` **[machine]** |

Fichiers en `kebab-case` (composants compris ; le nom du composant reste `PascalCase`).

## 2. Écrire une fonction et un composant

Seuils : fonction ≤ 40 lignes, imbrication ≤ 3, ≤ 3 paramètres positionnels (objet nommé au-delà), fichier ≤ 250 à 300 lignes.

1. **Séparer hook et présentation** : la requête et l'état vivent dans un hook ; le composant reçoit des données et rend du JSX.
2. **Un composant par fichier.**
3. **Garde d'abord** (`if (!x) return …`) plutôt que des blocs imbriqués.
4. **Booléens** : `isLoading`, `hasSelection`, `canValidate`. Pas de `boolean` muet en paramètre : objet nommé ou union.
5. **Nom métier** du `GLOSSARY.md`, pas `item`, `data`, `thing`.

```tsx
// À refuser : any, statut libre, branche oubliable
function Badge({ item }: { item: any }) {
  if (item.state === "DONE") return <b>Terminé</b>;
  return <b>{item.state}</b>;
}

// Attendu : type fermé, exhaustivité vérifiée par le compilateur
type State = "NEW" | "IN_PROGRESS" | "DONE";

function stateLabel(state: State): string {
  switch (state) {
    case "NEW": return "Nouveau";
    case "IN_PROGRESS": return "En cours";
    case "DONE": return "Terminé";
    default: return assertNever(state);
  }
}
```

Les valeurs réelles viennent du type généré depuis le contrat, jamais retapées à la main.

## 3. Types : rien de flou

- TypeScript `strict`. Pas de `any`, de `@ts-ignore`, d'`eslint-disable` sans raison écrite sur la ligne **[machine]**.
- Ce qui vient du réseau est typé par le **client généré**. Ce qui vient d'un formulaire est validé par **Zod** avant tout envoi. Une valeur `unknown` se restreint avant usage.
- États d'écran : union discriminée (`loading`, `error`, `data`) et `default: assertNever(...)` sur chaque `switch` d'union.
- Identifiants distincts = types distincts (type brandé) quand deux identifiants circulent dans la même fonction.
- `Result` pour un échec attendu côté client ; exception seulement pour un défaut de programmation.

## 4. Sécurité et confidentialité

- **Aucune règle métier.** Pas de calcul de permission, de filtre de cloisonnement ni de décision de transition. L'interface masque ce qu'on ne peut pas faire (confort) ; **l'API refuse**.
- **Pas de donnée sensible dans le navigateur** : ni `localStorage`, ni `sessionStorage`, ni IndexedDB, ni état global persistant.
- **`Cache-Control: no-store`** sur toute page qui affiche une donnée personnelle ou un document sensible ; un test vérifie l'en-tête.
- Variables d'environnement : ce qui est préfixé `NEXT_PUBLIC_` est compilé en clair dans le bundle public ; les secrets et URLs internes n'ont pas ce préfixe et ne vivent que dans des composants serveur ou des Server Actions. `import "server-only"` en tête des utilitaires qui manipulent des jetons.
- Une Server Action est un endpoint HTTP public : valider les entrées (Zod) et vérifier l'authentification à l'intérieur de la fonction.
- Ne jamais passer à un composant client l'objet brut renvoyé par l'API : filtrer dans un DTO côté serveur.
- En-têtes de sécurité (CSP avec nonce, `X-Content-Type-Options`, HSTS, `X-Frame-Options`) dans `next.config` ou le middleware.
- Session (cookie ou jeton) : décision d'ADR ; un jeton n'est jamais lisible par le JavaScript du navigateur (cookie `HttpOnly` si l'ADR le retient).

## 5. Tests

- Vitest + Testing Library pour les composants et hooks ; faux réseau (`msw`) ; Playwright pour les parcours critiques.
- On teste ce que voit et fait l'utilisateur, pas l'implémentation d'un hook.
- Un test d'autorisation ne remplace jamais ceux de l'API.
- Aucun test sauté, supprimé, affaibli pour passer au vert.

## 6. Code verbeux : ce qu'on retire

- Commentaire qui répète la ligne ; on garde le *pourquoi*.
- Composant enveloppe qui ne fait que transmettre ses props ; hook qui ne fait que renvoyer un autre hook.
- Abstraction « au cas où » ; trois usages semblables justifient d'extraire, deux non.
- Type dupliqué à la main quand le client généré ou un type dérivé (`Pick`, `Omit`, `ReturnType`) le donne.
- Dépendance ajoutée sans justification d'une phrase dans la PR, ou publiée depuis moins de 7 jours.

## 7. Définition de « terminé »

- [ ] `make ci-local` vert
- [ ] Aucun `any`, `@ts-ignore`, `console.log`, `fetch(` hors `lib/`
- [ ] Chaque `switch` sur une union se termine par `assertNever`
- [ ] Aucune règle métier ni décision d'autorisation dans le code
- [ ] Aucune donnée sensible dans le stockage du navigateur ; `no-store` sur les pages sensibles
- [ ] Un test échoue sans le changement
