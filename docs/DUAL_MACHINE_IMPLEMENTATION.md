# Dual-Machine Implementation for Trident Cloud Lab

## Decision

Run Trident Cloud Lab as a Tier B hybrid system first:

```text
public/cloud control plane
+ local red-rail research node
+ human review from the engineering workstation
```

Do not run the full lab as a cloud-only stack. The lab is evidence infrastructure,
and the sensitive parts belong inside the local/red boundary described in the
Kastel Stack docs.

## Machine Placement

### Machine 1: engineering workstation

Use for:

- VS Code and Codex development,
- GitHub commits, pull requests, and issue/project work,
- documentation edits,
- review of agent proposals,
- non-sensitive test runs,
- Obsidian exploration of public/internal notes if no restricted corpus is mounted.

Do not use as the only durable runtime for:

- raw participant data,
- private app telemetry,
- private PDFs or restricted paper notes,
- private embeddings,
- model artifacts that include sensitive source data.

### Machine 2: red-rail local node

In the Kastel reference stack this is the local trusted node, preferably a Mac
mini M4-class node or equivalent always-available local machine.

Run here:

- Postgres + pgvector for private research state,
- local object storage for private PDFs, exports, and model artifacts,
- local vector index for private/restricted corpus retrieval,
- redaction proxy for hosted LLM calls,
- local modeling workers for sensitive datasets,
- restricted Obsidian vaults if used,
- backup and restore jobs,
- optional Ollama/Open WebUI for local inference.

This is the correct home for the Cloud Lab core when it handles private evidence,
participant exports, unpublished notes, or restricted research material.

### Cloud/blue rail

Use for:

- GitHub as code/config truth,
- GitHub Actions for lint, tests, and public/synthetic scheduled checks,
- public website or experiment delivery,
- OSF/ResearchGate/public publication surfaces,
- Supabase only where the dataset and RLS/privacy posture are explicitly approved,
- hosted LLM calls only through redaction and tool allowlists.

Do not put raw restricted corpus, private embeddings, or unredacted participant
exports into cloud services by default.

## Why This Placement Fits Kastel Stack

Kastel Stack separates:

- blue rail: public publishing, distribution, external interfaces,
- red rail: sovereignty, local AI, private knowledge stores, restricted data,
- bridge-controlled outputs: sanitized research summaries and approved proof fragments.

Trident Cloud Lab belongs mainly to the evidence domain. In Kastel terms, outcomes
and evidence are red-first, with blue publication only after review and redaction.

That means:

```text
GitHub = code/config truth
local red node = private evidence runtime
cloud = public/runtime edge and approved automation
human review = publication and claim gate
```

## Component Placement

| Component | Default placement | Reason |
|---|---|---|
| Knowledge bank | red node for restricted corpus; GitHub for templates/docs | private notes and PDFs must not leak into public/cloud indexes |
| Zotero sync | workstation or red node | metadata may be low-risk; attachments and private notes stay local |
| OSF sync | workstation or red node | public metadata is fine; private/embargo content requires local handling |
| RAG retrieval | red node | private corpus and embeddings stay inside the boundary |
| Data pipeline | red node for raw/identified data; cloud only for approved pseudonymized flow | protects participant/app evidence |
| Experiment cloud | blue/cloud runtime, red-node evidence store | tasks may be public-facing; exports need governed handling |
| Computational modeling | red node for sensitive data; cloud only for synthetic/public runs | model artifacts can encode source data |
| Research agents | workstation/red node with review queue | agents propose; humans approve writes |
| Publication bridge | cloud/blue only after review | reviewed outputs can move to OSF, website, Substack, or papers |

## Data Flow

### 1. Theory and protocol

```text
Trident-G-theory repo
-> Cloud-Lab-Ground-Truth repo
-> research-platform protocol configs
-> reviewed runtime changes
```

The theory repo owns the protocol source. The ground-truth repo owns the component
map and claim boundaries. This runtime repo owns executable configs and code.

### 2. App telemetry

```text
app event export
-> schema validation
-> pseudonymization / consent check
-> red-node Postgres
-> analysis notebook/model run
-> reviewed proof fragment
-> blue publication only if approved
```

### 3. Knowledge and citations

```text
Zotero/OSF metadata
-> normalized records
-> generated markdown notes
-> local vector index
-> citation-grounded RAG
-> proposed claim/evidence link
-> human review
```

### 4. Experiment runtime

```text
GitHub protocol config
-> public/cloud experiment runtime
-> participant/session export
-> red-node validation and storage
-> analysis/model run
-> reviewed evidence output
```

## Implementation Phases

### Phase 0: local foundation

- Keep GitHub as code/config truth.
- Run Postgres + pgvector locally on the red node.
- Add `.env.example`, migrations, and schema validation.
- Use synthetic/sample data in GitHub Actions.

### Phase 1: knowledge sync

- Sync Zotero and OSF metadata.
- Generate markdown notes into the knowledge bank.
- Keep private attachments and restricted notes on the red node.
- Log sync runs and provenance.

### Phase 2: RAG and analysis

- Build embeddings on the red node for private corpus.
- Allow hosted LLM calls only through redaction.
- Store retrieval logs and citation ids.
- Keep private embeddings out of Git.

### Phase 3: telemetry and experiments

- Ingest one app export through schema validation.
- Run one baseline notebook.
- Deliver public experiment runtime from cloud only if data export and consent rules
  are approved.

### Phase 4: modeling and proof fragments

- Run sensitive modeling on the red node.
- Use cloud only for public/synthetic model checks.
- Promote evidence to publication only after claim-boundary review.

## Folder Notes

The current runtime repo has a useful split between `components/`, `infra/`,
`workflows/`, and `configs/`.

Recommended next cleanup:

- move `protocols/config/` to `protocol-configs/` or `configs/protocols/`,
- keep theory prose in the theory and ground-truth repos,
- keep this repo focused on executable runtime, schemas, migrations, notebooks,
  orchestration, and implementation docs.

## Non-Negotiables

1. No raw restricted data in Git.
2. No private embeddings in Git or public cloud by default.
3. No automated publication of claims.
4. No silent overwrites of manually edited research notes.
5. No hosted LLM call with unredacted restricted context.
6. Every evidence output must name its protocol version, source data, analysis
   version, and claim boundary.

