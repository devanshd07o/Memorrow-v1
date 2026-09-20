<div align="center">

  <br />
  <img src="assets/app_icon.png" alt="Memorrow Logo" width="108" height="108" style="border-radius: 24px; box-shadow: 0 20px 40px rgba(0,0,0,0.6);" />
  <br />
  <br />

  <h1>Memorrow</h1>

  <p><strong>The Obsidian-Dark, Local-First Productivity & Engineering OS</strong></p>

  <p>
    <a href="#the-problem--overview">Overview</a> •
    <a href="#key-architecture-highlights">Architecture</a> •
    <a href="#deep-dive-engine">Engine Deep-Dive</a> •
    <a href="#signature-feature-spotlight">Signature Spotlight</a> •
    <a href="#privacy--security-philosophy">Privacy</a> •
    <a href="#release-timeline--roadmap">Roadmap</a>
  </p>

  <p>
    <img src="https://img.shields.io/badge/Status-Private%20Beta%20%C2%B7%20Coming%20Soon-000000?style=for-the-badge&logo=rocket&logoColor=00F0FF&labelColor=0D0D12&color=1A1A24" alt="Status Badge" />
    <img src="https://img.shields.io/badge/Architecture-Offline--First%20%2B%20Cloud%20Sync-000000?style=for-the-badge&logo=sqlite&logoColor=00F0FF&labelColor=0D0D12&color=1A1A24" alt="Architecture Badge" />
    <img src="https://img.shields.io/badge/Security-Zero--Client--Secret%20PKCE-000000?style=for-the-badge&logo=letsencrypt&logoColor=00F0FF&labelColor=0D0D12&color=1A1A24" alt="Security Badge" />
  </p>

  <br />
</div>

---

## 📖 The Problem & Overview

Modern developers and power users are overwhelmed by fragmented productivity workflows. Critical tasks, LeetCode progress, project vaults, roadmap trackers, and AI assistants live across dozens of browser tabs and SaaS subscriptions. Furthermore, traditional productivity software heavily relies on cloud APIs—introducing **latency, privacy risks, and complete breakage when offline**.

**Memorrow** is engineered as a zero-compromise, cross-platform personal productivity OS for **Windows, macOS, Android, and iOS**. 

Built from the ground up on a **Local-First architecture**, Memorrow stores 100% of user data in high-performance local SQLite databases (`Drift`), providing instant sub-millisecond UI interactions and total offline autonomy. Cloud synchronization operates exclusively in the background through **Google Drive AppData**, ensuring zero vendor lock-in, zero external server tracking, and maximum user privacy.

---

## ⚡ Key Architecture Highlights

| Architectural Feature | Traditional SaaS Productivity Tools | Memorrow OS Architecture |
| :--- | :--- | :--- |
| **Data Ownership** | Stored on centralized third-party servers | **100% Local-First** (`Drift` / SQLite + AppData sync) |
| **Offline Functionality** | Broken or read-only | **Full Read/Write Autonomy** with instant local disk persistence |
| **Desktop OAuth Security** | Embedded client secrets or web redirects | **Zero-Client-Secret PKCE** via local loopback server |
| **Multi-Key AI Orchestration** | Fixed single API key bottleneck / Rate limits | **Atomic Parallel Race Load Balancer** across 50+ AI keys |
| **Cross-Device Sync** | Prone to duplicate item resurrection | **Tombstone Hard-Purge Sync** (`vault_v2_data_` array wipe) |
| **Mobile Integration** | Manual refresh / Passive web app | **Native Kotlin Reactive Widget Sync** for real-time state |

---

## 🛠️ Deep-Dive Engine & System Architecture

Memorrow follows a strict, audited **Clean Architecture Layering Protocol** across all supported desktop and mobile targets:

```mermaid
flowchart TD
    subgraph Presentation Layer ["Presentation Layer (Flutter UI & State)"]
        UI["Glassmorphic UI / Nav Rails / Overlays"]
        RP["Riverpod Notifiers & State Handlers"]
    end

    subgraph Domain Layer ["Domain Layer (Business Logic)"]
        UC["Use Cases & Domain Contracts"]
        MD["Models & Immutable Entities"]
    end

    subgraph Data Layer ["Data Layer (Local Storage & Cloud Sync)"]
        DB[("AppDatabase (Drift / SQLite local disk)")]
        SYNC["DriveSyncDataSource (Google Drive AppData)"]
        AI["GroqService / AI Orchestrator"]
    end

    UI <--> RP
    RP <--> UC
    UC <--> DB
    RP --> AI
    DB <--> SYNC
```

### Stage-by-Stage Data Flow Pipeline

1. **Local-First Execution Loop**: Any action (creating notes, marking roadmap targets, updating projects) writes instantly to local disk storage (`AppDatabase`). UI updates synchronously in **< 2 milliseconds**.
2. **Background Sync Engine**: Asynchronously checks local modified timestamps against the secure Google Drive AppData container (`vault_v2_data_$userId.json`). Changes are merged cleanly without blocking the UI.
3. **Zero-Resurrection Purge System**: Deletions perform direct array memory wipes (`removeWhere`), preventing tombstone resurrection across multi-device sync loops.

---

## 🌟 Signature Feature Spotlight

### 1. Multi-Key AI Load Balancer & Atomic Parallel Race

Memorrow features an advanced AI Orchestrator capable of pooling and managing **50+ API keys simultaneously** across multiple frontier providers (Groq, Gemini, Cerebras).

<div align="center">
  <img src="assets/ai_keys_management.png" alt="Multi-Key AI Management UI" width="90%" style="border-radius: 16px; border: 1px solid rgba(255,255,255,0.1);" />
  <p><em>Figure 1: Multi-Key API Management Interface supporting 50+ concurrent keys with auto-eviction.</em></p>
</div>

* **Atomic Parallel Race**: Fires user prompts across 5 active keys concurrently. The fastest responding key (~150-250ms) wins instantly while slower requests auto-cancel.
* **Instant 429 Cooldown Eviction**: If any single key encounters a rate limit, it receives an automatic 15-second cooldown block while the orchestrator instantly routes requests to remaining active keys.
* **Safe Mutex Lock**: Tool executions are protected by an atomic lock wrapper, guaranteeing zero duplicate note creations or navigation triggers during parallel key races.

---

### 2. Vault & Developer Health System

Organize codebases, credentials, private notes, and startup ideas inside a secure, encrypted Vault interface with real-time statistics tracking.

<div align="center">
  <img src="assets/vault_projects.png" alt="Developer Health & Vault Projects UI" width="90%" style="border-radius: 16px; border: 1px solid rgba(255,255,255,0.1);" />
  <p><em>Figure 2: Memorrow Vault & Developer Health Stats Showcase.</em></p>
</div>

* **LeetCode & GitHub Sync**: Automatic LeetCode slug matching and streak monitoring.
* **DSA Accordion Hierarchy**: Topic → Subtopic → Problem navigation designed specifically for technical interview preparation.
* **Glassmorphic Aesthetic**: Obsidian-dark semi-transparent panels, micro-animations, and backdrop blur.

---

## 🔒 Privacy & Security Philosophy

* **Zero-Telemetry Policy**: Memorrow collects zero user analytics, zero telemetry data, and zero behavioral metrics.
* **Zero-Client-Secret Desktop OAuth**: Desktop authentication (Windows/macOS) uses PKCE authorization code grant with a local loopback server (`http://127.0.0.1:<random-port>/oauth2redirect`). No client secrets are hardcoded or compiled into binaries.
* **Isolated Cloud Container**: Cloud backups live exclusively inside your personal Google Drive `appDataFolder`—invisible to other applications and strictly isolated to your authenticated account.

---

## 🗺️ Release Timeline & Roadmap

- [x] **v1.0.0 — Core Architecture**: Drift/SQLite local persistence + Clean Layering setup.
- [x] **v1.2.0 — Cloud Sync**: Background Google Drive AppData synchronization.
- [x] **v1.4.0 — Zero-Secret Auth**: Desktop PKCE Loopback OAuth2 integration.
- [x] **v1.6.0 — Multi-Key AI Engine**: Atomic Parallel Race & 50+ key load balancing.
- [ ] **v1.8.0 — Native Headless Sync**: Background OS daemon for Windows & macOS.
- [ ] **v2.0.0 — Public Release**: Multi-device public beta launch across Desktop & Mobile.

---

## 📄 License

This repository contains public showcase documentation and product specifications for **Memorrow**. 

Distributed under the **MIT License**. See `LICENSE` for more information.

---

<div align="center">
  <sub>Built with passion for high-performance software craftsmanship.</sub>
</div>
