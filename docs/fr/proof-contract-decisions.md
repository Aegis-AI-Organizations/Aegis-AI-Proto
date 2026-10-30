# Contrat de preuve - décisions à valider

| | |
|---|---|
| **Pour** | Charly (producteur Worker-Pentest / Agent-Crew), Noé (consommateur Dashboard) |
| **Porté par** | Antoine |
| **Document de référence** | [`proof-contract.md`](proof-contract.md) - `1.0.0` |
| **Objectif du point** | Passer le contrat de `1.0.0-draft` à `1.0.0` et débloquer les 3 P0 du chantier 1 |

Chaque décision est formulée comme une question fermée, avec la proposition du
brouillon. Il suffit de répondre **OK** ou de choisir une alternative.

## Résultat (2026-10-30)

Les options proposées ont été retenues pour D1 à D7 et sont implémentées
(contrat `1.0.0`). Charly et Noé valident en revue des PR ; toute décision
remise en cause donne une version suivante du contrat.

| Décision | Retenu | Précision apportée à l'implémentation |
|---|---|---|
| D1 | A - un seul `Finding` | `CrewPentestResult.findings` est typé `list[ContractFinding]`. |
| D2 | A - le producteur émet, le Brain valide | Le Crew vérifie ses extraits contre les sorties réelles de ses outils (§4.2 du contrat). |
| D3 | A - rejet + log | Log `🚫 scan=… rejected <source> finding: <raison>` dans le workflow. |
| D4 | Badge de niveau de preuve | Les findings `unverified` sont **affichés** avec un badge « Non vérifiée », pas masqués. |
| D5 | Evidence avec flag d'abord | Implémenté dans la requête `GetVulnerabilities`. |
| D6 | Dédup sur (`vuln_type`, `target_endpoint`) | S'applique aussi entre outils du Worker (`sqli-probe` + `sqlmap`). |
| D7 | Enum fermée en majuscules | Le Brain rejette toute autre valeur. |

Ajustement par rapport au brouillon : un finding `unverified` peut n'avoir
aucune evidence (une affirmation du Crew sans trace d'outil), au lieu d'être
supprimé. Il reste ainsi visible, avec le badge « Non vérifiée ».

---

## Le problème en une phrase

Une preuve trouvée par l'Agent-Crew n'arrive jamais dans le Dashboard :
`CrewPentestResult.findings` vaut `[]` dans tous les cas, la sortie du Crew
n'est que du Markdown, et le Brain ne persiste en vulnérabilités que les
résultats du Worker-Pentest.

---

## Décisions

### D1 - Le Crew émet-il dans le même schéma que le Worker ?

| Option | Conséquence |
|---|---|
| **A. Oui, un seul objet `Finding` (proposé)** | Une seule validation, une seule persistance, aucun changement côté Dashboard. Le Crew doit produire du JSON structuré. |
| B. Non, schéma Crew distinct | Deux chemins de persistance et deux formats à afficher. |

**Qui tranche :** Charly · **Impact Dashboard :** aucun avec A.

### D2 - Qui fait le mapping vers `Finding` ?

| Option | Conséquence |
|---|---|
| **A. Le producteur émet directement un `Finding` ; le Brain valide et rejette (proposé)** | Le mapping est fait là où l'information est disponible (la sortie de l'outil). Le Brain reste un point de contrôle, pas un traducteur. |
| B. Le Brain parse la sortie du Crew | Le Brain doit comprendre la sortie d'un LLM. Fragile, et la logique de preuve est dupliquée. |

**Qui tranche :** Charly + Antoine.

### D3 - Que fait le Brain d'un finding non conforme ?

| Option | Conséquence |
|---|---|
| **A. Rejet + log (proposé)** | Rien d'invalide n'arrive dans le Dashboard. Les rejets sont visibles dans les logs du scan. |
| B. Stockage en « quarantaine » | Nouvelle table ou nouveau statut, plus de travail, utile seulement si on veut afficher les rejets. |

### D4 - Les trois niveaux de preuve (`exploited` / `detected` / `unverified`)

Proposition :
- **`exploited`** : seul niveau qui compte pour le succès, c'est-à-dire que
  `aegis-flag-1234` a été extrait.
- **`detected`** : vulnérabilité avérée, par exemple par une erreur SQL.
- **`unverified`** : affirmation du Crew sans trace d'outil.

Questions pour **Noé** :
1. Le Dashboard doit-il afficher les findings `unverified`, ou les masquer par
   défaut ?
2. Un badge de niveau de preuve dans `VulnerabilityList` /
   `VulnerabilityDetailsDrawer` est-il faisable pour le MVP ?

Cela suppose d'ajouter `source` et `proof_level` au message `Vulnerability`
(proto, champs 9 et 10, changement **non cassant**).

### D5 - Quelle evidence alimente `loot_proof` quand il y en a plusieurs ?

Aujourd'hui : la plus récente par `captured_at`. Mais toutes les evidences d'un
même finding ont le même timestamp, donc le choix est aléatoire, et une erreur
SQL peut masquer un flag extrait.

**Proposé :** d'abord l'evidence avec `seed_flag_extracted == true`, sinon la
plus récente.

### D6 - Doublons Worker / Crew sur la même vulnérabilité

**Proposé :** même finding si même (`vuln_type`, `target_endpoint`). On garde
celui qui a la preuve la plus forte, à égalité celui du Worker, et on fusionne
les evidences.

### D7 - Sévérités

**Proposé :** enum fermée `CRITICAL | HIGH | MEDIUM | LOW | INFO`, déjà
utilisée par le Worker et le Dashboard. La tâche de rapport du Crew (qui
demande `High/Medium/Low`) est alignée.

---

## Ce que chaque décision débloque

| P0 | Débloqué par | Dépôt | Travail derrière |
|---|---|---|---|
| **P0-1** Le Crew produit des findings structurés | D1, D2, D7 | Agent-Crew | Modèle Pydantic `Finding`, `output_pydantic` sur la tâche d'exécution, `findings: list[Finding]`, `response_excerpt` issu des outils. |
| **P0-2** Le Brain ingère les findings du Crew | D2, D3, D6 | Brain | Validation à l'ingestion, appel de `save_vulnerabilities` sur Worker + Crew, nouvelle clé de dédup. |
| **P0-3** La preuve du Crew est visible dans le Dashboard | D4, D5 | Proto, Brain, Dashboard | `source` / `proof_level` dans le proto et en base, choix d'evidence corrigé, badge Dashboard. |

---

## Après le point

1. ~~Reporter les réponses dans `proof-contract.md`, passer en `1.0.0`.~~ Fait.
2. ~~Corriger les écarts du §10 du brouillon.~~ Fait ; les limites restantes
   sont au §10 du contrat.
3. Ajouter un lien vers le contrat depuis la doc Agent-Crew, Brain et Dashboard
   d'`Aegis-AI-Documentation`.

---

*Ingénierie des contrats Aegis AI - 2026*
