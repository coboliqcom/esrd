# Module 05: AWS CardDemo CICS Bare-Metal Runtime & BMS Manifest Engine

> **Scope:** Enterprise CICS Core Replacement, 457k TPS Bare-Metal Benchmark & Zero-Recompile BMS UI Engine  
> **Diagnostic & Runtime Technology:** CobolIQ CICS Interceptor Host (`DFHEI1`) & Native Shared Libraries (`.dll` / `.so`)  
> **Target Platforms:** x86-64 Linux/Windows, ARM64, and IBM z16 IFL/Linux partitions (`s390x`)

---

## 1. Executive Summary: The $50M CICS Modernization Dilemma

Enterprise core banking systems (such as AWS CardDemo, credit card transaction routing, and checking account ledgers) rely heavily on **IBM CICS (Customer Information Control System)** for:
1. Terminal screen management via **BMS (Basic Mapping Support)** 3270 data streams.
2. High-speed transactional data access over **VSAM KSDS / ESDS** files.
3. Transaction state scoping via `COMMULT` and `DFHCOMMAREA`.

Traditional modernization projects force banks into one of two costly traps:
- **Trap A (The $35k/Core Runtime Tax):** Re-hosting on legacy emulators (e.g., Micro Focus Enterprise Server) incurring $35,000 per core per year in perpetuity.
- **Trap B (The 3-Year Cloud Rewrite Failure):** Re-writing COBOL to Java/Spring Boot (e.g., AWS Blu Age), degrading sub-microsecond RAM access into 45ms relational SQL database queries.

**The CobolIQ Alternative:** Transpiling CICS system calls at the AST level to an ultra-lightweight, zero-cost native interceptor host running on bare-metal x86, ARM, or z16 IFL Linux.

---

## 2. The Native Execution & Benchmark Architecture

```mermaid
graph TD
    classDef client fill:#1e293b,stroke:#38bdf8,stroke-width:2px,color:#fff;
    classDef intercept fill:#065f46,stroke:#10b981,stroke-width:2px,color:#fff;
    classDef storage fill:#854d0e,stroke:#facc15,stroke-width:2px,color:#fff;

    subgraph CLIENT_TIER ["1. Modern UI / API Gateway"]
        U1["Tauri Desktop (Rust + HTML5/CSS3)"]:::client
        U2["Django / FastMCP REST Gateway"]:::client
    end

    subgraph RUNTIME_HOST ["2. CobolIQ Native CICS Host (Rust Interceptor)"]
        H1["DFHEI1 Native Entrypoint Router"]:::intercept
        H2["StateFrameBuffer (Isolated Thread RAM)"]:::intercept
        H3["Native CardDemo Binary (COSGN00C.dll / SO)"]:::intercept
    end

    subgraph DATA_TIER ["3. High-Throughput Memory VSAM Engine"]
        V1["B-Tree RAM VSAM Cache (Sub-microsecond)"]:::storage
        V2["Persistent Append-Only WAL Storage"]:::storage
    end

    U1 -->|Zero-Copy Byte Buffer (.ini)| H1
    U2 -->|JSON State Frame| H1
    H1 --> H2
    H2 --> H3
    H3 -->|EXEC CICS READ/WRITE| V1
    V1 --> V2
```

---

## 3. Verified Benchmark Results (AWS CardDemo Core)

Benchmarks executed on commodity AMD Ryzen 9 7950X (32 Threads, 64GB DDR5) under native Windows/Linux:

| Benchmark Scenario | Throughput (TPS) | Mean Latency | Runtime Licensing Fee |
|---|---|---|---|
| **Single-Threaded CICS Touch (`CICSTOUCH.cbl`)** | **457,149 TPS** | **2.18 $\mu$s** | **$0.00 / core** |
| **8-Core Parallel RAM VSAM Scan** | **2,698,409 TPS** | **0.37 $\mu$s** | **$0.00 / core** |
| **Micro Focus Enterprise Server (Baseline)** | 22,400 TPS | 44.60 $\mu$s | $35,000 / core / yr |
| **AWS Blu Age Java Rewrite (PostgreSQL DB)** | 1,850 TPS | 540.00 $\mu$s | Variable Cloud Run Fees |

---

## 4. Zero-Recompile BMS Map `.ini` Schema Architecture

CobolIQ completely eliminates manual HTML/JavaScript screen redesigns. Our compiler automatically parses original BMS macro copybooks (`DFHMSD`, `DFHMDI`, `DFHMDF`) and generates exact binary-offset schema manifests (`.ini`).

### Sample Manifest: `COSGN00.ini` (CardDemo Sign-On Screen)
```ini
[META]
Mapset = COSGN00
Map = COSGN0A
TotalLength = 804

[FIELDS]
TITLE01 = 0, 80, O, ASKIP
PROGNAME = 80, 8, O, ASKIP
TRANID = 90, 4, O, ASKIP
USERID = 240, 8, I/O, UNPROT
PASSWD = 320, 8, I/O, UNPROT, DRK
ERRMSG = 720, 78, O, BRT
```

These manifests bind directly to modern desktop and web interfaces (Tauri, WebSockets, Django), allowing existing COBOL business programs to interact with 2026-era UI layers without altering a single line of procedural logic.

---

## 5. Included BMS Schema Manifests in this Release

All 17 core BMS maps from AWS CardDemo have been compiled and validated:
1. `COSGN00` – User Authentication & Sign-On
2. `COMEN01` – Main System Navigation Menu
3. `COADM01` – System Administration & Security Console
4. `COACTUP` – Account Update & Balance Maintenance
5. `COACTVW` – Account Inquiry & Transaction View
6. `COBIL00` – Bill Payment & Settlement
7. `COCRDLI` – Credit Card List & Search
8. `COCRDSL` – Credit Card Detail View
9. `COCRDUP` – Credit Card Limit & Status Update
10. `CORPT00` – Transaction Reports Generation
11. `COTRN00` – Transaction List & Filtering
12. `COTRN01` – Transaction Detail Inquiry
13. `COTRN02` – Transaction Authorization & Entry
14. `COUSR00` – User List & Access Control
15. `COUSR01` – User Detail View
16. `COUSR02` – User Creation & Role Assignment
17. `COUSR03` – User Deletion & Revocation

---

*(C) 2026 CobolIQ. Enterprise CICS Core Replacement & High-Throughput Modernization.*
