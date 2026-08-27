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
