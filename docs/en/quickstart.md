# Proto Quickstart

`Aegis-AI-Proto` is the single source of truth for the gRPC contracts shared
between the API Gateway (Go) and the Brain (Python).

---

## Generate stubs

From the repository root:

```bash
buf generate
```

Generated code is consumed by:

- `Aegis-AI-Api-Gateway` — Go gRPC clients (`internal/grpc/`).
- `Aegis-AI-Brain` — Python gRPC services (`src/`).

In CI, `proto-sync.yml` regenerates on push to `main` and pushes the updated
stubs into both consumer repositories automatically.

---

## Lint

```bash
buf lint
buf breaking --against '.git#branch=main'
```

---

## Change workflow

1. Edit the `.proto` definition.
2. Regenerate stubs (`buf generate`).
3. Update the Gateway handlers and the Brain services together.
4. Update `openapi.yaml` in `Aegis-AI-Documentation` when the contract is exposed
   over REST.
5. Add or update tests in both consumers.

---

## Compatibility checklist

- Field numbers are stable.
- New fields are optional or have safe defaults.
- Removals / renumbering / semantic changes are coordinated across Gateway,
  Brain, tests, and OpenAPI.
- Error semantics and tenant/auth metadata requirements are documented.

---

*Aegis AI Contract Engineering — 2026*
