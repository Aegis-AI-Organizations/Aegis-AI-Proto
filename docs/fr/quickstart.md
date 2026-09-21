# Quickstart Proto

`Aegis-AI-Proto` est la source de vérité unique des contrats gRPC partagés entre
l'API Gateway (Go) et le Brain (Python).

---

## Générer les stubs

Depuis la racine du dépôt :

```bash
buf generate
```

Le code généré est consommé par :

- `Aegis-AI-Api-Gateway` — clients gRPC Go (`internal/grpc/`).
- `Aegis-AI-Brain` — services gRPC Python (`src/`).

En CI, `proto-sync.yml` régénère à chaque push sur `main` et pousse
automatiquement les stubs mis à jour dans les deux dépôts consommateurs.

---

## Lint

```bash
buf lint
buf breaking --against '.git#branch=main'
```

---

## Workflow de changement

1. Modifier la définition `.proto`.
2. Régénérer les stubs (`buf generate`).
3. Mettre à jour ensemble les handlers de la Gateway et les services du Brain.
4. Mettre à jour `openapi.yaml` dans `Aegis-AI-Documentation` lorsque le contrat
   est exposé en REST.
5. Ajouter ou adapter les tests dans les deux consommateurs.

---

## Checklist de compatibilité

- Les numéros de champs sont stables.
- Les nouveaux champs sont optionnels ou ont des valeurs par défaut sûres.
- Les suppressions / renumérotations / changements de sémantique sont coordonnés
  entre Gateway, Brain, tests et OpenAPI.
- La sémantique des erreurs et les exigences de métadonnées tenant/auth sont
  documentées.

---

*Ingénierie des contrats Aegis AI — 2026*
