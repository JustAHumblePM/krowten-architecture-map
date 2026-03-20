# Krowten Architecture Map

An interactive diagram of the Krowten platform — Little Dot Studios' media processing and distribution pipeline that automates video preparation and publishing to YouTube at scale.

**Live diagram:** [justahumblepm.github.io/krowten-architecture-map](https://justahumblepm.github.io/krowten-architecture-map/)

## What is this?

This tool scans all Krowten-related repositories from the Little Dot Studios GitHub organisation, extracts structural information about each service via static analysis, maps out how they interact, and renders an interactive system diagram. It tracks changes over time by re-scanning daily and diffing.

## What does it show?

- **38 microservices** that make up the Krowten pipeline
- **Connections between services** — Cloud Tasks, Pub/Sub, shared GCS buckets, Firestore collections, and HTTP calls
- **Data flow annotations** — what kind of data (video files, transcripts, metadata, etc.) flows through each connection
- **Service descriptions** — click any node for a plain-English explanation of what it does
- **API routes** — every HTTP endpoint exposed by each service
- **Change tracking** — a changelog of what changed between daily scans

## How to use the diagram

- **Click a node** to open its detail panel (description, routes, connections)
- **Hover over an edge** to see what data flows through it
- **Use the legend** (top-left) to filter by connection type
- **Search** (top bar) to find a specific service
- **Layout button** to cycle between graph layouts (fCOSE, circle, grid)
- **Fit button** to reset the view
- **Changelog button** to see recent changes across all services

## The Krowten Pipeline

The services map to stages in this processing flow:

1. **Ingest** — Source assets from Rightsline with eligible territory rights
2. **Restore** — Trigger restoration from deep storage in Ikonik (MAM)
3. **Transfer** — Move video files from Ikonik's AWS backend to GCS
4. **Transcribe** — Run OpenAI Whisper on parallel Compute Engine VMs
5. **Metadata Generation** — AI generates YouTube titles, tags, and descriptions
6. **Channel Matching** — Semantic search to match assets to best-fit YouTube channels
7. **Policy Matching** — Map territory rights to YouTube CMS policy names
8. **Frame Extraction** — Split video into segments, extract candidate frames
9. **Aesthetic Scoring** — Score frames for visual quality, flag top candidates
10. **Heroshot** — Human picks the best frame for each asset+channel combination
11. **Sidekick** — Node-based thumbnail template editor (effects, overlays, text)
12. **Thumbnail Rendering** — Apply channel template to selected frame
13. **Upload** — Upload video, metadata, and thumbnail to YouTube
14. **Scheduling** — Channel-level rules dictate posting cadence and priority

## How it works

- A Python scanner clones/pulls all Krowten repos and runs static analysis (Python AST parsing, regex, file parsing)
- Each repo produces a compact JSON manifest (~1-2KB) capturing routes, dependencies, GCP resources, and environment variables
- A graph builder infers edges between services based on shared resources (queues, topics, buckets, collections, HTTP calls)
- The result is exported as Cytoscape.js-compatible JSON and rendered as an interactive diagram
- A daily scheduled scan re-analyses all repos, diffs against the previous scan, and auto-deploys updates to this page

## Tech stack

- **Scanner:** Python (AST, regex, YAML/TOML parsing)
- **Graph:** Cytoscape.js with fCOSE layout
- **Hosting:** GitHub Pages (auto-deployed daily)
- **Platform under analysis:** GCP (Cloud Run, Pub/Sub, GCS, Firestore, Compute Engine, BigQuery)
