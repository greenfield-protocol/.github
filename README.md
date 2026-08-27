# .github — configuration commune de l'organisation GreenField

> Dépôt 3 de l'arborescence polyrepo ([`greenfield-plan/docs/07`](../greenfield-plan/docs/07-architecture-depots.md)).
> Il ne contient aucun code produit : il contient ce qui doit être **identique partout**.

## Pourquoi ce dépôt existe

C'est l'une des **deux règles qui rendent le polyrepo viable en solo** :

> *La configuration commune est centralisée dans un seul dépôt, et les autres l'importent via des workflows réutilisables. On n'entretient jamais six fichiers CI parallèles.*

Sans lui, chaque dépôt dérive : les seuils de couverture divergent, `gitleaks` n'est actif que sur trois dépôts sur six, et personne ne s'en aperçoit avant l'incident.

## Contenu

```
.github/
├── workflows/
│   ├── reusable-foundry.yml     appele par greenfield-contracts
│   ├── reusable-python.yml      appele par greenfield-api        (phase 2)
│   ├── reusable-node.yml        appele par indexer / web         (phase 2-3)
│   └── reusable-security.yml    gitleaks + semgrep, TOUS depots
├── ISSUE_TEMPLATE/
└── PULL_REQUEST_TEMPLATE.md     la checklist de docs/09 §9.5
```

## Comment un dépôt l'utilise

```yaml
# greenfield-contracts/.github/workflows/ci.yml
name: CI
on: [push, pull_request]
permissions:
  contents: read          # minimum, toujours (docs/09 §9.4)
jobs:
  foundry:
    uses: greenfield-protocol/.github/.github/workflows/reusable-foundry.yml@main
  security:
    uses: greenfield-protocol/.github/.github/workflows/reusable-security.yml@main
```

## Règles non négociables, appliquées ici pour tous

| Règle | Pourquoi |
|---|---|
| **Actions épinglées par SHA**, jamais par tag | Un tag se déplace ; un SHA non |
| `permissions: contents: read` par défaut | Le jeton par défaut de GitHub est trop large |
| **`gitleaks` bloquant** sur tous les dépôts | La fuite de secret est la plus fréquente et la plus bête |
| Jamais de `pull_request_target` sans revue | C'est la voie d'exécution de code non revu avec les secrets du dépôt |
| Verrous de dépendances versionnés | Un build non déterministe n'est pas auditable |

## État

**Squelette.** Les workflows réutilisables sont écrits au fur et à mesure que les dépôts naissent ([`docs/07`](../greenfield-plan/docs/07-architecture-depots.md) §7.6) — créer six CI avant d'avoir du code utile est un excellent moyen de s'épuiser avant de commencer.

## Workflows réutilisables

Deux workflows, appelés par les dépôts de l'organisation. Les centraliser ici est
l'une des deux règles qui rendent le polyrepo viable en solo : **on n'entretient
jamais six fichiers CI parallèles.**

| Workflow | Ce qu'il fait |
|---|---|
| `reusable-foundry.yml` | `forge fmt --check`, `forge build --sizes`, `forge test`, couverture avec **seuil bloquant par fichier**, et `forge snapshot --check` pour attraper les régressions de gaz silencieuses |
| `reusable-securite.yml` | Motifs sensibles, mentions d'assistance IA dans l'historique, et — sur les dépôts Solidity — quatre règles propres au protocole |

### Les quatre règles propres au protocole

Chacune ferme un constat d'audit nommé. **Une barrière CI dont on ne sait pas quel
scénario elle ferme est une barrière qu'on désactivera le jour où elle gêne.**

| Règle | Ferme |
|---|---|
| `Deal` n'accorde **jamais** d'allocation — pas d'`approve`, `increaseAllowance` ni `permit` | **I-68** (constat D-04). Une allocation est une sortie de fonds différée : elle ne viole ni la comptabilité interne, ni la règle sur `token.transfer` |
| Aucun `delegatecall`, aucun `selfdestruct` | **I-27**. `Deal` n'est jamais upgradable et n'a pas de propriétaire |
| **Exactement un** appel à `token.transfer` dans `src/` | **ADR-021**. Les quatre décisions de sortie *créditent* une comptabilité ; seule `withdraw()` transfère réellement |
| Lexique du domaine — ni `validator`, ni `buyer`, ni `seller`… | **GLOSSARY**. Un synonyme n'est pas une élégance de style : c'est une ambiguïté qui produit un bug que la revue ne voit pas |

### Le scan de secrets n'utilise aucune action externe

`gitleaks-action` exige une licence pour les organisations, et **une barrière qui
échoue pour une raison administrative finit désactivée.** Sept motifs sont donc
exécutés directement par `git grep`, sans réseau et sans dépendance.

Un motif a dû être resserré dès l'écriture : `token[[:space:]]*=` attrapait
`token = new MockERC20()` dans les tests. La valeur doit désormais être **entre
guillemets**. Une barrière qui crie au loup est une barrière qu'on finit par ignorer.

### Dette connue

Les actions sont épinglées **par tag** (`@v4`, `@v1`) et non par SHA, ce que
[`docs/09`](https://github.com/greenfield-protocol/greenfield-plan) §9.4 exige.
Les SHA n'ont pas pu être vérifiés au moment de l'écriture, et inventer un SHA
aurait cassé la CI. À corriger : `DETTE(2026-09-30, docs/09)`.

