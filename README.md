# 📜 Aegis AI - Protocol & Inter-service Spec (Proto)

**Project ID:** AEGIS-CORE-2026

## 🏗️ System Architecture & Role
The **Aegis AI Proto** repository is the central source of truth for all cross-service communication contracts. Our event-driven microservices architecture mandates strict type-safety and serialized formats for inter-service gRPC communication.

* **Tech Stack:** Protocol Buffers (v3).
* **Role:** Defines the `.proto` schemas allowing seamless integration between the Rust Agents, Go API Gateways, and Python Penetration/Brain clusters.

## 🔐 Security & DevSecOps Mandates
All services relying on these protocol definitions must implement data over **mTLS** streams. This protocol repo acts as the sole standardizer of messages.

### Example Contract Snippet
```protobuf
syntax = "proto3";
package aegis.telemetry.v1;

service LogCollector {
  rpc StreamLogs (stream LogEntry) returns (Ack);
}

message LogEntry {
  int64 timestamp = 1;
  string level = 2; // INFO, WARN, ERROR
  string service_id = 3;
  bytes payload = 4; // Compressed binary blob
}
```

## 🐳 Compilation & Deployment
This repository does not map directly to a running Docker deployment. Instead, a `buf` build pipeline generates language-specific bindings (Rust, Go, Python) that are consumed as dependencies by the respective worker container images.
