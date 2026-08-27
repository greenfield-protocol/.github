## Ce que fait cette PR

<!-- Une phrase. Si elle en demande trois, la PR fait trois choses. -->

## Checklist — docs/09 §9.5

- [ ] Les noms respectent `GLOSSARY.md` et `docs/09`
- [ ] Aucun montant en `float` / `number` ; unites mineures partout, suffixe `Minor`
- [ ] **Aucune nouvelle voie de sortie de fonds** (sinon : 2 relecteurs + `THREAT_MODEL.md` mis a jour)
- [ ] `Deal` n accorde aucune allocation ; le token n est reference que dans `fund()`/`openDispute()` et `withdraw()`
- [ ] Entrees validees a la frontiere
- [ ] Aucun secret, aucune PII dans le code, les logs ou les tests
- [ ] Tests ajoutes, dont **un cas d echec** et **un cas limite**
- [ ] Documentation a jour (NatSpec / docstring / JSDoc / ADR si decision structurante)
- [ ] `JOURNAL.md` complete si la session apporte quelque chose de racontable
- [ ] CI verte : lint, types, tests, securite

## Invariants touches

<!-- Lesquels, et comment ils restent vrais. « Aucun » est une reponse valable
     si c est vrai — mais toute PR touchant aux fonds en touche au moins un. -->

## Ce que je n ai PAS verifie

<!-- Obligatoire. Une PR sans cette section est une PR qui pretend etre complete. -->
