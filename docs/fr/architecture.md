# Architecture de l'Écosystème Protobuf (v2)

Ce dépôt (`Aegis-AI-Proto`) constitue la source de vérité unique régissant toutes les communications inter-microservices au sein de la **Plateforme Aegis AI**. Il utilise `buf` pour orchestrer la compilation des protocol buffers et gère la génération centralisée des stubs gRPC.

## Version 2 (MVP)
Dans l'architecture MVP, le domaine Aegis s'appuie unilatéralement sur **gRPC via HTTP/2** pour le trafic névralgique entre l'API Gateway et l'orchestrateur central (Brain).

### Définitions des Services
- **`aegis.v2.ScanService`** : Contrôle le cycle de vie des scans de sécurité (`StartScan`, `GetScanStatus`, `ListScans`, `GetScanReport`).
- **`aegis.v2.VulnerabilityService`** : Indexe les conclusions et preuves cryptographiques de piratage (`GetVulnerabilities`, `GetEvidences`).
- **`aegis.v2.PingService`** : Valide d'un simple ping la connectivité de bout en bout sur le cluster interne.

### CI/CD Code Sync Automatique
Étant donné le couplage contractuel très fort induit par le gRPC, ce référentiel embarque une pipeline d'auto-déploiement.
À chaque fusion sur `main`, `.github/workflows/proto-sync.yml` recompile les `.proto` de la `v2` en fichiers Go et Python. Pour garantir une compatibilité d'écosystème radicale, le runner détruit chirurgicalement les contremesures de versions des runtimes insérées par la génération. Les stubs tout beaux tout neufs sont ensuite poussés aux bénéficiaires :
- `Aegis-AI-Api-Gateway` (Implémentations Go)
- `Aegis-AI-Brain` (Implémentations Python)
