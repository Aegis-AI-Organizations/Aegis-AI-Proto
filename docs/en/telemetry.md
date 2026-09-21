# Proto Telemetry

The proto repository emits no runtime telemetry. Its operational value is
**contract traceability**.

---

## What to track

- Generated-stub version referenced in Gateway and Brain commits.
- Breaking-change review outcomes before merge.
- `buf generate` / `proto-sync.yml` CI failures.
- Consumer test failures after a contract change.

## Documentation link

Any REST-exposed proto change must be reflected in `openapi.yaml` and in the
relevant module documentation of `Aegis-AI-Documentation`.

---

*Aegis AI Contract Engineering — 2026*
