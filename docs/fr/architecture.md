# [FR] # Architecture | Aegis-AI-Proto

Cette documentation présente l'architecture "Pure Microservices" mise en place pour le projet Aegis AI.

## Vue d'Ensemble
L'infrastructure s'articule autour d'une séparation stricte des responsabilités afin d'améliorer la scalabilité et l'isolation des dépendances.

1. **Aegis-AI-Api-Gateway** (Le Front)
   - Agit exclusivement comme **Proxy gRPC**.
   - Ne possède **plus** de connexion à la base de données PostgreSQL.
   - Ne communique **pas** avec le cluster Temporal.
   - Toutes les requêtes HTTP (REST) entrantes sont converties en appels gRPC vers le backend métier.

2. **Aegis-AI-Brain** (Le Core Métier)
   - Héberge un serveur **gRPC** pour réceptionner les commandes de l'API Gateway.
   - Responsable exclusif des écritures et lectures dans **PostgreSQL** (`psycopg`).
   - S'occupe de la communication avec **Temporal** pour lancer les workflows d'orchestration (`PentestWorkflow`).

3. **Aegis-AI-Proto** (Les Contrats)
   - Contient les définitions Protobuf (`scan.proto`, `vulnerability.proto`, `ping.proto`) utilisées pour la génération du code asynchrone Go et Python.
