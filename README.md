<div align="center">

  <br />
  <img src="assets/app_icon.png" alt="Memorrow Logo" width="120" height="120" style="border-radius: 28px; box-shadow: 0 20px 50px rgba(0,0,0,0.8);" />
  <br />
  <br />

  <h1>Memorrow</h1>

  <p><strong>The Obsidian-Dark, Local-First Productivity & Engineering OS</strong></p>

  <p>
    <a href="#-overview">Overview</a> •
    <a href="#-agentic-ai-engine">Agentic AI</a> •
    <a href="#-key-architecture-highlights">Architecture</a> •
    <a href="#-system-diagrams">System Diagrams</a> •
    <a href="#-signature-feature-spotlight">Spotlight</a> •
    <a href="#-privacy--security">Privacy</a> •
    <a href="#-roadmap">Roadmap</a>
  </p>

  <p>
    <img src="https://img.shields.io/badge/Status-Private%20Beta%20%C2%B7%20v1.6.0-000000?style=for-the-badge&logo=rocket&logoColor=00F0FF&labelColor=0D0D12&color=1A1A24" alt="Status Badge" />
    <img src="https://img.shields.io/badge/AI Engine-Agentic%20Tool%20Fulfillment-000000?style=for-the-badge&logo=openai&logoColor=00F0FF&labelColor=0D0D12&color=1A1A24" alt="AI Engine Badge" />
    <img src="https://img.shields.io/badge/Storage-100%25%20Local--First%20Drift-000000?style=for-the-badge&logo=sqlite&logoColor=00F0FF&labelColor=0D0D12&color=1A1A24" alt="Storage Badge" />
    <img src="https://img.shields.io/badge/Security-PKCE%20Zero--Secret%20OAuth-000000?style=for-the-badge&logo=letsencrypt&logoColor=00F0FF&labelColor=0D0D12&color=1A1A24" alt="Security Badge" />
  </p>

  <br />

  <img src="assets/hero_dashboard.png" alt="Memorrow Hero Dashboard" width="100%" style="border-radius: 16px; border: 1px solid rgba(255,255,255,0.12); box-shadow: 0 30px 60px rgba(0,0,0,0.7);" />
  <p><em>Figure 1: Memorrow Obsidian-Dark Command Center — Real-time DSA Progress, Focus Tracks, & Activity Heatmap.</em></p>

  <br />
</div>

---

## 📖 Overview

Modern software engineers and power users face severe workflow fragmentation. DSA preparation, codebase roadmaps, system design notes, private credentials, and AI prompts are scattered across browser tabs and cloud subscriptions. Furthermore, traditional cloud SaaS tools introduce **API latency, privacy invasion, and total outage when offline**.

**Memorrow** is engineered as a zero-compromise, cross-platform productivity OS for **Windows, macOS, Android, and iOS**.

Built on a strict **Local-First Architecture**, Memorrow persists 100% of user state in high-performance local SQLite databases (`Drift`), delivering instant **sub-millisecond UI responsiveness**. Cloud synchronization operates invisibly in the background via **Google Drive AppData**, guaranteeing zero vendor lock-in, zero external telemetry, and absolute privacy.

---

## 🤖 Agentic AI Engine & Tool Capabilities

Memorrow integrates **`AgentRunner`**, a autonomous multi-turn agentic framework that transforms raw user prompts into executable application actions without manual user intervention.

```mermaid
flowchart LR
    UserPrompt["User Prompt / Slash Command"] --> Orchestrator["AI Orchestrator Engine"]
    Orchestrator --> ParallelRace{"5-Key Parallel Race (~150ms)"}
    ParallelRace --> WinnerKey["First Responder Wins"]
    WinnerKey --> AgentRunner["AgentRunner Loop"]
    
    AgentRunner --> ToolParse{"Contains Tool Call?"}
    ToolParse -- Yes --> ExecTool["Execute App State Mutation"]
    ToolParse -- Plain Markdown --> AutoFulfill["Turn-1 Instant Synthetic Fulfillment"]
    
    ExecTool --> UIUpdate["Reactive Glassmorphic UI Update"]
    AutoFulfill --> UIUpdate
```

### Key Agentic Capabilities & Slash Commands

| Slash Command / Intent | Action Performed | Agentic Execution Flow |
| :--- | :--- | :--- |
| `/createnote <title>` | Creates structured, rich-text note | Generates full markdown hierarchy (overview, code snippets, practice problems, takeaways) and writes to local SQLite in < 200ms. |
| `/appendnote <title>` | Appends content to existing note | Locates existing note by fuzzy title match and appends fresh sections without duplication. |
| `/markdone <item_id>` | Updates roadmap progress | Identifies target DSA/Backend topic, marks item completed, and updates native Android widgets reactively. |
| `/addidea <title>` | Saves encrypted Vault idea | Verifies Vault unlock state and persists project ideas into encrypted memory storage. |
| `/navigate <tab>` | Switches application view | Controls main app navigation (`Home`, `Roadmap`, `Calendar`, `Notes`, `Leagues`, `Vault`, `Settings`). |
| `/createproject` | Initializes Gold Card project | Creates complete portfolio project schema with tech stack, problem statement, and ATS keywords. |

### Smart Agentic Features
* **Turn-1 Synthetic Fulfillment**: If an LLM returns complete markdown content without explicit `<tool_call>` tags during a note creation request, `AgentRunner` automatically synthesizes the tool invocation on Turn 1—saving notes in **2 milliseconds** instead of triggering 4-turn retry loops.
* **Atomic Mutex Lock**: Tool actions are wrapped in an atomic execution lock (`safeToolCall`), preventing duplicate note creations or state mutations during 5-key concurrent races.
* **Context Injection**: Every prompt dynamically embeds local calendar events, active DSA targets, LeetCode streak stats, and personalized user instructions.

---

## ⚡ Key Architecture Highlights

| System Component | Traditional SaaS Productivity Tools | Memorrow OS Architecture |
| :--- | :--- | :--- |
| **Data Storage** | Centralized third-party cloud servers | **100% Local-First** (`Drift` / SQLite + background Drive sync) |
| **Offline Mode** | Broken or read-only | **Full Autonomy** — sub-millisecond local disk reads & writes |
| **AI Performance** | Single key queue / Rate limit blocks | **50+ Key Parallel Race Load Balancer** (~150ms latency) |
| **Desktop OAuth** | Hardcoded secrets or external browsers | **Zero-Client-Secret PKCE** via local loopback server |
| **DSA Hierarchy** | Flat lists or basic tags | **Accordion Hierarchy** (Topic → Subtopic → Problem) |
| **Mobile Integration** | Manual pull-to-refresh | **Native Kotlin Reactive Widget Sync** |

---

## 📐 System Diagrams & Deep-Dive Architecture

### 1. Multi-Key AI Load Balancing & Failover Architecture

Memorrow manages a candidate pool of **50+ API keys** across multiple frontier providers (Groq, Gemini, Cerebras, OpenRouter, SambaNova).

```mermaid
sequenceDiagram
    autonumber
    actor User as User / Prompt
    participant AO as AI Orchestrator
    participant Pool as 5-Key Concurrent Fan-Out
    participant Service as Winning Key (Groq Llama 3.3 70B)
    participant Agent as AgentRunner Engine
    participant DB as AppDatabase (SQLite)

    User->>AO: Send Prompt ("create note on Binary Trees")
    AO->>Pool: Spawn 5 Concurrent API Key Requests
    Pool-->>Service: Candidate #1 Responds in 160ms (WINNER)
    Pool-->>AO: Cancel slower candidate requests
    AO->>Agent: Process Response Stream
    Agent->>DB: Execute create_note Tool Call (< 2ms)
    DB-->>User: Render Glassmorphic Note Block in UI
```

---

### 2. Local-First Synchronization & Tombstone Purge Engine

```mermaid
flowchart TD
    subgraph Client ["Client Device (Local Disk)"]
        UI_Event["UI Action (Add / Delete Item)"]
        SQLite[("AppDatabase (SQLite)")]
        LocalCache["Memory State / Riverpod"]
    end

    subgraph SyncEngine ["Background Sync Engine"]
        TimestampCheck{"Compare Local vs Cloud Timestamp"}
        PurgeFilter["Tombstone Hard-Purge (vault_v2_data_)"]
    end

    subgraph Cloud ["Google Drive AppData Container"]
        DriveFile[("vault_v2_data_$userId.json")]
    end

    UI_Event -->|Instant Write < 2ms| SQLite
    SQLite --> LocalCache
    LocalCache --> TimestampCheck
    TimestampCheck -- Local Newer --> PurgeFilter
    PurgeFilter -->|Async Write| DriveFile
    DriveFile -- Pull Updates --> TimestampCheck
```

---

### 3. Zero-Client-Secret PKCE Desktop OAuth Flow

```mermaid
sequenceDiagram
    autonumber
    participant App as Memorrow Desktop App
    participant Server as Local Loopback Server (http://127.0.0.1:port)
    participant Browser as System Default Browser
    participant Google as Google Identity Server

    App->>Server: Start Loopback Listener on Random Port
    App->>Browser: Launch Auth URL with PKCE Code Challenge
    Browser->>Google: Authenticate & Authorize Scope
    Google->>Server: HTTP Redirect to /oauth2redirect?code=AUTH_CODE
    Server->>App: Handshake Complete
    App->>Google: Exchange AUTH_CODE + Code Verifier for Tokens
    Google-->>App: Return Access Token & Refresh Token (Zero Client Secret!)
```

---

## 🌟 Signature Feature Spotlight

### 1. Multi-Key AI Orchestrator & Load Balancer Interface

<div align="center">
  <img src="assets/ai_keys_management.png" alt="Multi-Key AI Management UI" width="92%" style="border-radius: 16px; border: 1px solid rgba(255,255,255,0.12);" />
  <p><em>Figure 2: Multi-Key API Management supporting 50+ concurrent keys with 15-second automatic rate-limit cooldown.</em></p>
</div>

* **Concurrent Key Fan-Out**: Fires prompts to 5 active candidate keys simultaneously. The fastest response (~150ms) wins, while remaining requests are safely discarded.
* **Automatic Cooldown Eviction**: Encounters with HTTP `429 Rate Limit` automatically isolate the affected key for a 15-second cooldown period, preventing queue congestion.

---

### 2. Vault & Developer Health Stats

<div align="center">
  <img src="assets/vault_projects.png" alt="Developer Health & Vault Projects UI" width="92%" style="border-radius: 16px; border: 1px solid rgba(255,255,255,0.12);" />
  <p><em>Figure 3: Developer Health Stats & Encrypted Vault Projects Section.</em></p>
</div>

* **Project Gold Cards**: Complete portfolio tracking including tech stack, problem statement, launch date, live URL, and ATS keywords.
* **Encrypted Vault**: PIN-locked storage for private credentials, system ideas, identity details, and Forge Lab experiments.

---

### 3. Obsidian Dark Calendar & Activity Heatmap

<div align="center">
  <img src="assets/calendar_heatmap.png" alt="Obsidian Dark Calendar Heatmap" width="92%" style="border-radius: 16px; border: 1px solid rgba(255,255,255,0.12);" />
  <p><em>Figure 4: Dark Glassmorphic Calendar & Continuous Learning Heatmap.</em></p>
</div>

---

## 🔒 Privacy & Security

* **Zero Telemetry**: Memorrow contains zero user tracking, zero analytics collection, and zero external tracking scripts.
* **Isolated Drive Storage**: Backup files reside exclusively inside your Google Drive `appDataFolder`—completely hidden from third-party applications and accessible only by you.
* **Zero Client Secret**: Desktop binaries contain zero compiled secrets or API keys.

---

## 🗺️ Roadmap

- [x] **v1.0.0 — Core Architecture**: Drift/SQLite local persistence + Clean Layering.
- [x] **v1.2.0 — Cloud Sync Engine**: Background Google Drive AppData sync.
- [x] **v1.4.0 — Zero-Secret Auth**: Desktop PKCE Loopback OAuth2 authentication.
- [x] **v1.6.0 — Multi-Key AI Engine**: AgentRunner tool fulfillment & 50+ key load balancing.
- [ ] **v1.8.0 — Background OS Daemon**: System tray daemon for Windows & macOS.
- [ ] **v2.0.0 — Public Beta Launch**: Cross-platform desktop & mobile release.

---

## 📄 License

Distributed under the **MIT License**. See `LICENSE` for details.

---

<div align="center">
  <sub>Memorrow OS — Engineered for speed, privacy, and developer productivity.</sub>
</div>
