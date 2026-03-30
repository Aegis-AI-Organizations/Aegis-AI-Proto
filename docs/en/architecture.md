# Protobuf Ecosystem Architecture (v2)

This repository (`Aegis-AI-Proto`) is the single source of truth for all inter-microservice communication within the **Aegis AI Platform**. It uses `buf` to orchestrate protocol buffer compilation and manages the centralized generation of gRPC stubs.

## Version 2 (MVP)
In the MVP architecture, the Aegis framework relies exclusively on **gRPC over HTTP/2** for communication between the API Gateway and the Brain orchestrator.

### Service Definitions
- **`aegis.v2.ScanService`**: Controls the lifecycle of security scans (`StartScan`, `GetScanStatus`, `ListScans`, `GetScanReport`).
- **`aegis.v2.VulnerabilityService`**: Retrieves the findings and cryptographic evidence (`GetVulnerabilities`, `GetEvidences`).
- **`aegis.v2.PingService`**: Validates end-to-end connectivity across the cluster.

### CI/CD Code Sync
Because protocol buffers tightly couple the orchestrators, this repository features an auto-sync pipeline.
Whenever pushing to `main`, `.github/workflows/proto-sync.yml` automatically compiles the `v2` `.proto` definitions into Go and Python stubs. It inherently strips out troublesome runtime version validation statements from generated files to ensure extreme ecosystem compatibility, and actively propagates (pushes) the code to:
- `Aegis-AI-Api-Gateway` (Golang implementations)
- `Aegis-AI-Brain` (Python implementations)
