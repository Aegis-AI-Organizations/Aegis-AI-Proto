# Télémétrie Proto

Le dépôt proto n'émet aucune télémétrie runtime. Sa valeur opérationnelle est la
**traçabilité des contrats**.

---

## Ce qu'il faut suivre

- La version des stubs générés référencée dans les commits de la Gateway et du
  Brain.
- Les résultats des revues de changements cassants avant merge.
- Les échecs CI de `buf generate` / `proto-sync.yml`.
- Les échecs de tests des consommateurs après un changement de contrat.

## Lien avec la documentation

Tout changement de proto exposé en REST doit être répercuté dans `openapi.yaml` et
dans la documentation du module concerné d'`Aegis-AI-Documentation`.

---

*Ingénierie des contrats Aegis AI — 2026*
