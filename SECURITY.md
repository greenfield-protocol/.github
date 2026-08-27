# Politique de sécurité — GreenField

## Signaler une faille

Écrivez à **security@** *(adresse à créer — voir REPRISE.md)*.
Accusé de réception sous **48 heures**.

Merci de ne pas ouvrir d'issue publique pour une vulnérabilité affectant un contrat
déployé détenant de la valeur.

## Portée

| Dépôt | Statut |
|---|---|
| `greenfield-contracts` | ⚠️ **Aucun déploiement, sur aucun réseau.** Ce code n'a jamais détenu de valeur |
| Les autres | N'existent pas encore |

## Ce que nous publions déjà

Deux audits de sécurité complets, **conservés non corrigés**, sont publics dans
[`greenfield-plan/spec/audits/`](../greenfield-plan/spec/audits/) — 53 constats,
dont 11 critiques, la plupart trouvés **avant** qu'une ligne de Solidity soit écrite.

Nous les publions parce qu'un protocole de séquestre qui cache ses audits ne mérite
pas qu'on lui confie de l'argent. Vous y trouverez aussi ce que nous n'avons **pas**
encore corrigé, et pourquoi.

## Engagement

Tout incident affectant des fonds est publié avec un post-mortem, quelle qu'en soit
la cause.

## Bug bounty

Ouvert au déploiement mainnet, barème indexé sur la valeur bloquée. Pas avant :
récompenser des failles sur un code que personne n'utilise détourne l'attention
de celles qui compteront.
