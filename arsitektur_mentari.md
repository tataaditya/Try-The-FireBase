# Dokumentasi Diagram Sistem MENTARI

## Implementasi AI Agent Orchestration untuk Efisiensi Administrasi Strahan Kementerian Pertahanan

---

# Diagram 1: System Architecture Diagram (System Context Diagram)

## Judul Formal
**Diagram Arsitektur Sistem AI Agent Orchestration MENTARI**

## Tujuan Diagram
Diagram ini menggambarkan arsitektur keseluruhan sistem MENTARI sebagai platform AI Agent Orchestration yang berjalan secara on-premise. Diagram menunjukkan hubungan antar komponen utama, aliran data, dan protokol komunikasi yang digunakan dalam sistem untuk mendukung otomasi administrasi dokumen di lingkungan Kementerian Pertahanan.

## Komponen Sistem

| No | Komponen | Deskripsi | Teknologi |
|----|----------|-----------|-----------|
| 1 | User | Pengguna akhir (staf administrasi) | - |
| 2 | Chainlit Web Interface | Antarmuka pengguna berbasis web | Chainlit (Python) |
| 3 | MENTARI AI Agent | Agen AI utama untuk orkestrasi | Python, LangChain |
| 4 | Keyword Router | Modul deteksi intent berbasis keyword | Python |
| 5 | Document Handler | Modul penanganan operasi dokumen | Python |
| 6 | MCP Manager | Manajer koneksi ke MCP Servers | Python, MCP SDK |
| 7 | Ollama Server | Server LLM lokal | Ollama |
| 8 | LLM Model | Model bahasa besar | Qwen 2.5 Coder |
| 9 | MCP Word Server | Server MCP untuk dokumen Word | Python |
| 10 | MCP Excel Server | Server MCP untuk spreadsheet | Python |
| 11 | MCP PowerPoint Server | Server MCP untuk presentasi | Python |
| 12 | MCP PDF Server | Server MCP untuk dokumen PDF | Python |
| 13 | Local File Storage | Penyimpanan dokumen lokal | Filesystem |

## Hubungan Antar Komponen

| Dari | Ke | Tipe Komunikasi | Protokol |
|------|-----|-----------------|----------|
| User | Chainlit Web Interface | Request/Response | HTTP/WebSocket |
| Chainlit Web Interface | MENTARI AI Agent | Function Call | Internal |
| MENTARI AI Agent | Keyword Router | Function Call | Internal |
| Keyword Router | Document Handler | Intent Routing | Internal |
| Document Handler | Ollama Server | REST API | HTTP (localhost:11434) |
| Document Handler | MCP Manager | Function Call | Internal |
| MCP Manager | MCP Word Server | JSON-RPC | stdio |
| MCP Manager | MCP Excel Server | JSON-RPC | stdio |
| MCP Manager | MCP PowerPoint Server | JSON-RPC | stdio |
| MCP Manager | MCP PDF Server | JSON-RPC | stdio |
| MCP Servers (All) | Local File Storage | File I/O | Filesystem |

## Layout
**Rekomendasi:** Top-Down dengan 5 layer horizontal

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        LINGKUNGAN ON-PREMISE (LOKAL)                        │
├─────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                     LAYER 1: USER INTERFACE                         │    │
│  │  ┌──────────┐         ┌──────────────────────────┐                  │    │
│  │  │   User   │◄───────►│   Chainlit Web Interface │                  │    │
│  │  └──────────┘  HTTP   └──────────────────────────┘                  │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                    │                                        │
│                                    ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                  LAYER 2: AI AGENT ORCHESTRATION                    │    │
│  │  ┌──────────────────────────────────────────────────────────────┐   │    │
│  │  │                    MENTARI AI Agent                          │   │    │
│  │  │  ┌────────────────┐  ┌──────────────────┐  ┌──────────────┐  │   │    │
│  │  │  │ Keyword Router │─►│ Document Handler │─►│  MCP Manager │  │   │    │
│  │  │  └────────────────┘  └──────────────────┘  └──────────────┘  │   │    │
│  │  └──────────────────────────────────────────────────────────────┘   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                    │                              │                         │
│          HTTP REST │                    JSON-RPC  │                         │
│                    ▼                              ▼                         │
│  ┌──────────────────────┐    ┌─────────────────────────────────────────┐    │
│  │   LAYER 3: LLM       │    │         LAYER 4: MCP SERVERS            │    │
│  │  ┌────────────────┐  │    │  ┌─────────┐ ┌─────────┐ ┌───────────┐  │    │
│  │  │ Ollama Server  │  │    │  │MCP Word │ │MCP Excel│ │MCP PowerPt│  │    │
│  │  │  ┌──────────┐  │  │    │  └────┬────┘ └────┬────┘ └─────┬─────┘  │    │
│  │  │  │Qwen 2.5  │  │  │    │       │           │            │        │    │
│  │  │  │ Coder    │  │  │    │  ┌────┴───────────┴────────────┴────┐   │    │
│  │  │  └──────────┘  │  │    │  │           MCP PDF Server         │   │    │
│  │  └────────────────┘  │    │  └──────────────────────────────────┘   │    │
│  └──────────────────────┘    └─────────────────────────────────────────┘    │
│                                              │                              │
│                                              ▼                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                     LAYER 5: LOCAL STORAGE                          │    │
│  │           ┌─────────────────────────────────────┐                   │    │
│  │           │      Local File Storage (USER_FILES)│                   │    │
│  │           │   .docx  .xlsx  .pptx  .pdf  .txt   │                   │    │
│  │           └─────────────────────────────────────┘                   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Mermaid Syntax

```mermaid
flowchart TB
    subgraph ENV["Lingkungan On-Premise"]
        direction TB
        
        subgraph L1["Layer 1: User Interface"]
            USER["User<br/>(Staf Administrasi)"]
            UI["Chainlit Web Interface<br/>(localhost:8000)"]
        end
        
        subgraph L2["Layer 2: AI Agent Orchestration"]
            AGENT["MENTARI AI Agent"]
            ROUTER["Keyword Router"]
            HANDLER["Document Handler"]
            MCPMGR["MCP Manager"]
        end
        
        subgraph L3["Layer 3: Local LLM"]
            OLLAMA["Ollama Server<br/>(localhost:11434)"]
            MODEL["Qwen 2.5 Coder"]
        end
        
        subgraph L4["Layer 4: MCP Servers"]
            MCPW["MCP Word Server"]
            MCPE["MCP Excel Server"]
            MCPP["MCP PowerPoint Server"]
            MCPPDF["MCP PDF Server"]
        end
        
        subgraph L5["Layer 5: Local Storage"]
            STORAGE[("Local File Storage<br/>USER_FILES/")]
        end
    end
    
    USER <-->|"HTTP/WebSocket"| UI
    UI --> AGENT
    AGENT --> ROUTER
    ROUTER --> HANDLER
    HANDLER --> MCPMGR
    HANDLER <-->|"HTTP REST API"| OLLAMA
    OLLAMA --> MODEL
    MCPMGR <-->|"JSON-RPC (stdio)"| MCPW
    MCPMGR <-->|"JSON-RPC (stdio)"| MCPE
    MCPMGR <-->|"JSON-RPC (stdio)"| MCPP
    MCPMGR <-->|"JSON-RPC (stdio)"| MCPPDF
    MCPW <--> STORAGE
    MCPE <--> STORAGE
    MCPP <--> STORAGE
    MCPPDF <--> STORAGE
```

---

# Diagram 2: Use Case Diagram

## Judul Formal
**Diagram Use Case Sistem AI Agent Orchestration MENTARI**

## Tujuan Diagram
Diagram ini mengidentifikasi aktor-aktor yang berinteraksi dengan sistem MENTARI beserta use case (kasus penggunaan) yang tersedia. Diagram menggambarkan fungsionalitas utama sistem dari perspektif pengguna untuk mendukung otomasi administrasi dokumen.

## Aktor

| No | Aktor | Deskripsi |
|----|-------|-----------|
| 1 | Staf Administrasi | Pengguna utama yang membuat dan mengelola dokumen |
| 2 | Sistem MENTARI | Aktor sekunder (sistem AI Agent) |

## Use Cases

| No | Use Case ID | Nama Use Case | Deskripsi |
|----|-------------|---------------|-----------|
| UC-01 | CREATE_WORD | Membuat Dokumen Word | Membuat dokumen Word baru dengan konten yang di-generate AI |
| UC-02 | CREATE_EXCEL | Membuat Spreadsheet Excel | Membuat spreadsheet Excel dengan data tabular |
| UC-03 | CREATE_PPT | Membuat Presentasi PowerPoint | Membuat presentasi dengan slide yang di-generate AI |
| UC-04 | READ_FILE | Membaca Isi Dokumen | Membaca dan menampilkan konten dokumen (PDF/Word) |
| UC-05 | SUMMARIZE | Merangkum Dokumen | Merangkum isi dokumen menggunakan AI |
| UC-06 | CONVERT_PDF | Mengkonversi ke PDF | Mengkonversi dokumen Word menjadi format PDF |
| UC-07 | UPLOAD_FILE | Mengunggah Dokumen | Mengunggah dokumen ke sistem untuk diproses |
| UC-08 | LIST_FILES | Melihat Daftar Dokumen | Menampilkan daftar dokumen yang tersedia |
| UC-09 | CHAT | Berkomunikasi dengan AI | Berinteraksi dengan AI untuk pertanyaan umum |

## Hubungan Use Case

| Tipe | Use Case Utama | Use Case Terkait | Keterangan |
|------|----------------|------------------|------------|
| Include | UC-01, UC-02, UC-03 | Generate Content (AI) | Pembuatan dokumen memerlukan AI untuk generate konten |
| Include | UC-05 | UC-04 | Merangkum memerlukan pembacaan dokumen terlebih dahulu |
| Extend | UC-01 | UC-06 | Setelah membuat Word, dapat mengkonversi ke PDF |
| Extend | UC-07 | UC-04, UC-05 | Setelah upload, dapat membaca atau merangkum |

## Layout
**Rekomendasi:** Left-Right dengan aktor di kiri dan use cases di tengah

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          SISTEM MENTARI                                     │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                                                                       │  │
│  │     ┌─────────────────────────────────────────────┐                   │  │
│  │     │  ○ UC-01: Membuat Dokumen Word              │                   │  │
│  │     └─────────────────────────────────────────────┘                   │  │
│  │                          │                                            │  │
│  │     ┌─────────────────────────────────────────────┐                   │  │
│  │     │  ○ UC-02: Membuat Spreadsheet Excel         │                   │  │
│  │     └─────────────────────────────────────────────┘                   │  │
│  │                                                                       │  │
┌──┴──┐  ┌─────────────────────────────────────────────┐                   │  │
│     │  │  ○ UC-03: Membuat Presentasi PowerPoint     │                   │  │
│ 👤  │──┤─────────────────────────────────────────────┤                   │  │
│Staf │  │  ○ UC-04: Membaca Isi Dokumen               │◄──────┐           │  │
│Admin│  └─────────────────────────────────────────────┘       │           │  │
│     │                                                   <<include>>      │  │
└──┬──┘  ┌─────────────────────────────────────────────┐       │           │  │
│  │     │  ○ UC-05: Merangkum Dokumen                 │───────┘           │  │
│  │     └─────────────────────────────────────────────┘                   │  │
│  │                                                                       │  │
│  │     ┌─────────────────────────────────────────────┐                   │  │
│  │     │  ○ UC-06: Mengkonversi ke PDF               │                   │  │
│  │     └─────────────────────────────────────────────┘                   │  │
│  │                                                                       │  │
│  │     ┌─────────────────────────────────────────────┐                   │  │
│  │     │  ○ UC-07: Mengunggah Dokumen                │                   │  │
│  │     └─────────────────────────────────────────────┘                   │  │
│  │                                                                       │  │
│  │     ┌─────────────────────────────────────────────┐                   │  │
│  │     │  ○ UC-08: Melihat Daftar Dokumen            │                   │  │
│  │     └─────────────────────────────────────────────┘                   │  │
│  │                                                                       │  │
│  │     ┌─────────────────────────────────────────────┐                   │  │
│  │     │  ○ UC-09: Berkomunikasi dengan AI           │                   │  │
│  │     └─────────────────────────────────────────────┘                   │  │
│  │                                                                       │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Mermaid Syntax

```mermaid
flowchart LR
    subgraph ACTORS["Aktor"]
        USER["👤 Staf Administrasi"]
    end
    
    subgraph SYSTEM["Sistem MENTARI"]
        UC01(("UC-01<br/>Membuat Dokumen Word"))
        UC02(("UC-02<br/>Membuat Spreadsheet Excel"))
        UC03(("UC-03<br/>Membuat Presentasi PowerPoint"))
        UC04(("UC-04<br/>Membaca Isi Dokumen"))
        UC05(("UC-05<br/>Merangkum Dokumen"))
        UC06(("UC-06<br/>Mengkonversi ke PDF"))
        UC07(("UC-07<br/>Mengunggah Dokumen"))
        UC08(("UC-08<br/>Melihat Daftar Dokumen"))
        UC09(("UC-09<br/>Berkomunikasi dengan AI"))
        
        GEN(("Generate Content"))
    end
    
    USER --- UC01
    USER --- UC02
    USER --- UC03
    USER --- UC04
    USER --- UC05
    USER --- UC06
    USER --- UC07
    USER --- UC08
    USER --- UC09
    
    UC01 -.->|"<<include>>"| GEN
    UC02 -.->|"<<include>>"| GEN
    UC03 -.->|"<<include>>"| GEN
    UC05 -.->|"<<include>>"| UC04
```

---

# Diagram 3: Activity Diagram (Main Workflow)

## Judul Formal
**Diagram Aktivitas Alur Kerja Utama Sistem MENTARI**

## Tujuan Diagram
Diagram ini menggambarkan alur aktivitas utama dalam sistem MENTARI, mulai dari input pengguna hingga output dokumen. Diagram menunjukkan urutan langkah-langkah, keputusan kondisional, dan aktivitas paralel dalam proses pembuatan dokumen otomatis.

## Daftar Aktivitas

| No | Aktivitas | Deskripsi |
|----|-----------|-----------|
| A1 | Start | Titik awal proses |
| A2 | Menerima Input Pengguna | Sistem menerima perintah dari pengguna |
| A3 | Mendeteksi Intent | Keyword Router menganalisis jenis permintaan |
| A4 | Memproses Intent | Document Handler menangani permintaan berdasarkan intent |
| A5 | Generate Konten AI | LLM menghasilkan konten teks |
| A6 | Memanggil MCP Server | MCP Manager memanggil server yang sesuai |
| A7 | Membuat Dokumen | MCP Server membuat/memodifikasi dokumen |
| A8 | Menyimpan ke Storage | Dokumen disimpan ke local storage |
| A9 | Menampilkan Hasil | Sistem menampilkan hasil ke pengguna |
| A10 | End | Titik akhir proses |

## Decision Points

| No | Keputusan | Kondisi | Cabang |
|----|-----------|---------|--------|
| D1 | Tipe Intent? | CREATE_WORD / CREATE_EXCEL / CREATE_PPT / READ_FILE / CONVERT_PDF / CHAT | 6 cabang |
| D2 | Perlu AI Content? | Ya / Tidak | 2 cabang |
| D3 | Operasi Berhasil? | Sukses / Gagal | 2 cabang |

## Layout
**Rekomendasi:** Top-Down dengan decision diamonds

```
                              ┌───────┐
                              │ START │
                              └───┬───┘
                                  │
                                  ▼
                    ┌─────────────────────────────┐
                    │   Menerima Input Pengguna   │
                    └─────────────┬───────────────┘
                                  │
                                  ▼
                    ┌─────────────────────────────┐
                    │     Mendeteksi Intent       │
                    │     (Keyword Router)        │
                    └─────────────┬───────────────┘
                                  │
                                  ▼
                        ┌─────────────────┐
                       ╱                   ╲
                      ╱   Tipe Intent?      ╲
                     ╱                       ╲
                    ╱─────────────────────────╲
                   │                           │
      ┌────────────┼───────────┬───────────────┼────────────┐
      │            │           │               │            │
      ▼            ▼           ▼               ▼            ▼
┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
│  CREATE  │ │  CREATE  │ │  CREATE  │ │   READ   │ │  CONVERT │
│   WORD   │ │  EXCEL   │ │   PPT    │ │   FILE   │ │   PDF    │
└────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘
      │            │           │               │            │
      └────────────┴─────┬─────┴───────────────┴────────────┘
                         │
                         ▼
               ┌─────────────────┐
              ╱                   ╲
             ╱  Perlu Generate    ╲
            ╱   Konten AI?         ╲
           ╱                        ╲
          ╱──────────────────────────╲
         │ Ya                      Tidak│
         ▼                              │
┌─────────────────────────┐             │
│  Generate Konten AI     │             │
│  (Ollama LLM)           │             │
└───────────┬─────────────┘             │
            │                           │
            └───────────┬───────────────┘
                        │
                        ▼
          ┌─────────────────────────────┐
          │   Memanggil MCP Server      │
          │   (JSON-RPC)                │
          └─────────────┬───────────────┘
                        │
                        ▼
          ┌─────────────────────────────┐
          │   Membuat/Memproses         │
          │   Dokumen                   │
          └─────────────┬───────────────┘
                        │
                        ▼
              ┌─────────────────┐
             ╱                   ╲
            ╱  Operasi Berhasil?  ╲
           ╱                       ╲
          ╱─────────────────────────╲
         │ Ya                    Tidak│
         ▼                            ▼
┌───────────────────┐      ┌───────────────────┐
│Simpan ke Storage  │      │ Tampilkan Error   │
└─────────┬─────────┘      └─────────┬─────────┘
          │                          │
          └──────────┬───────────────┘
                     │
                     ▼
       ┌─────────────────────────────┐
       │   Menampilkan Hasil ke      │
       │   Pengguna                  │
       └─────────────┬───────────────┘
                     │
                     ▼
                ┌─────────┐
                │   END   │
                └─────────┘
```

## Mermaid Syntax

```mermaid
flowchart TD
    START([Start]) --> A1[Menerima Input Pengguna]
    A1 --> A2[Mendeteksi Intent<br/>Keyword Router]
    A2 --> D1{Tipe Intent?}
    
    D1 -->|CREATE_WORD| B1[Proses Word]
    D1 -->|CREATE_EXCEL| B2[Proses Excel]
    D1 -->|CREATE_PPT| B3[Proses PowerPoint]
    D1 -->|READ_FILE| B4[Proses Baca File]
    D1 -->|CONVERT_PDF| B5[Proses Konversi]
    D1 -->|CHAT| B6[Proses Chat]
    
    B1 --> D2{Perlu Generate<br/>Konten AI?}
    B2 --> D2
    B3 --> D2
    B4 --> D2
    B5 --> D2
    B6 --> A3[Generate Response AI]
    
    D2 -->|Ya| A3[Generate Konten AI<br/>via Ollama LLM]
    D2 -->|Tidak| A4
    A3 --> A4[Memanggil MCP Server]
    
    A4 --> A5[Membuat/Memproses Dokumen]
    A5 --> D3{Operasi<br/>Berhasil?}
    
    D3 -->|Ya| A6[Simpan ke Local Storage]
    D3 -->|Tidak| A7[Generate Error Message]
    
    A6 --> A8[Menampilkan Hasil ke Pengguna]
    A7 --> A8
    
    A8 --> END([End])
```

---

# Diagram 4: Swimlane Diagram (Responsibility-Based Workflow)

## Judul Formal
**Diagram Swimlane Pembagian Tanggung Jawab Sistem MENTARI**

## Tujuan Diagram
Diagram ini menggambarkan alur kerja sistem MENTARI dengan pembagian tanggung jawab berdasarkan komponen/layer sistem. Setiap lane merepresentasikan sebuah komponen yang bertanggung jawab atas aktivitas tertentu dalam proses pembuatan dokumen.

## Swimlanes (Lanes)

| No | Lane | Komponen | Tanggung Jawab |
|----|------|----------|----------------|
| 1 | User Interface | Chainlit Web UI | Menerima input, menampilkan output |
| 2 | AI Agent | MENTARI Agent | Orkestrasi proses, routing intent |
| 3 | LLM Service | Ollama Server | Generate konten menggunakan AI |
| 4 | MCP Layer | MCP Servers | Operasi dokumen Office |
| 5 | Storage | Local Filesystem | Penyimpanan dokumen |

## Aktivitas per Lane

| Lane | Aktivitas |
|------|-----------|
| User Interface | Terima Input → Validasi → Kirim ke Agent → Terima Hasil → Tampilkan ke User |
| AI Agent | Deteksi Intent → Tentukan Handler → Koordinasi MCP → Compile Response |
| LLM Service | Terima Prompt → Proses Model → Generate Text → Return Response |
| MCP Layer | Terima Command → Execute Tool → Create/Read File → Return Status |
| Storage | Receive Write → Save File → Confirm → Retrieve File → Send Content |

## Layout
**Rekomendasi:** Horizontal lanes dengan flow left-to-right

```
┌────────────────────────────────────────────────────────────────────────────────────────────┐
│                                                                                            │
│  ┌──────────────┐                                                                          │
│  │     User     │   ┌─────────┐                                           ┌─────────────┐ │
│  │  Interface   │   │ Terima  │                                           │  Tampilkan  │ │
│  │  (Chainlit)  │   │ Input   │──────────────────────────────────────────►│   Hasil     │ │
│  └──────────────┘   └────┬────┘                                           └─────────────┘ │
│                          │                                                       ▲        │
├──────────────────────────┼───────────────────────────────────────────────────────┼────────┤
│  ┌──────────────┐        │                                                       │        │
│  │   AI Agent   │        ▼                                                       │        │
│  │  (MENTARI)   │   ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐     │        │
│  │              │   │ Deteksi │───►│ Routing │───►│Koordinasi───►│ Compile │─────┘        │
│  └──────────────┘   │ Intent  │    │ Handler │    │   MCP   │    │Response │              │
│                     └─────────┘    └────┬────┘    └────┬────┘    └─────────┘              │
│                                         │              │                                   │
├─────────────────────────────────────────┼──────────────┼───────────────────────────────────┤
│  ┌──────────────┐                       │              │                                   │
│  │ LLM Service  │                       ▼              │                                   │
│  │   (Ollama)   │               ┌───────────────┐      │                                   │
│  │              │               │ Generate Text │      │                                   │
│  └──────────────┘               │   Content     │      │                                   │
│                                 └───────────────┘      │                                   │
│                                                        │                                   │
├────────────────────────────────────────────────────────┼───────────────────────────────────┤
│  ┌──────────────┐                                      │                                   │
│  │  MCP Layer   │                                      ▼                                   │
│  │ (MCP Servers)│                              ┌───────────────┐    ┌───────────────┐      │
│  │              │                              │ Execute Tool  │───►│ Create/Read   │      │
│  └──────────────┘                              │ (JSON-RPC)    │    │   Document    │      │
│                                                └───────────────┘    └───────┬───────┘      │
│                                                                             │              │
├─────────────────────────────────────────────────────────────────────────────┼──────────────┤
│  ┌──────────────┐                                                           │              │
│  │   Storage    │                                                           ▼              │
│  │(Local Files) │                                                   ┌───────────────┐      │
│  │              │                                                   │  Save/Read    │      │
│  └──────────────┘                                                   │    File       │      │
│                                                                     └───────────────┘      │
│                                                                                            │
└────────────────────────────────────────────────────────────────────────────────────────────┘
```

## Mermaid Syntax

```mermaid
flowchart LR
    subgraph UI["User Interface (Chainlit)"]
        direction LR
        U1[Terima Input] --> U2[Validasi Request]
        U3[Tampilkan Hasil]
    end
    
    subgraph AGENT["AI Agent (MENTARI)"]
        direction LR
        A1[Deteksi Intent] --> A2[Routing Handler]
        A2 --> A3[Koordinasi MCP]
        A3 --> A4[Compile Response]
    end
    
    subgraph LLM["LLM Service (Ollama)"]
        direction LR
        L1[Terima Prompt] --> L2[Generate Content]
        L2 --> L3[Return Text]
    end
    
    subgraph MCP["MCP Layer (Servers)"]
        direction LR
        M1[Terima Command] --> M2[Execute Tool]
        M2 --> M3[Process Document]
    end
    
    subgraph STORAGE["Storage (Local Files)"]
        direction LR
        S1[Write File] --> S2[Confirm Save]
    end
    
    U2 --> A1
    A2 --> L1
    L3 --> A3
    A3 --> M1
    M3 --> S1
    S2 --> A4
    A4 --> U3
```

---

# Diagram 5: Flowchart (Internal AI Agent Decision Logic)

## Judul Formal
**Flowchart Logika Keputusan Internal AI Agent MENTARI**

## Tujuan Diagram
Diagram ini menggambarkan logika keputusan internal yang terjadi di dalam komponen AI Agent MENTARI. Flowchart menunjukkan bagaimana sistem menentukan tindakan berdasarkan keyword yang terdeteksi pada input pengguna, serta alur penanganan error dan fallback.

## Langkah-langkah Proses

| No | Step ID | Nama Langkah | Tipe | Deskripsi |
|----|---------|--------------|------|-----------|
| 1 | S01 | Start | Terminal | Awal proses |
| 2 | S02 | Receive Message | Process | Menerima pesan dari pengguna |
| 3 | S03 | Check File Upload | Decision | Cek apakah ada file yang diunggah |
| 4 | S04 | Process Upload | Process | Proses penyimpanan file upload |
| 5 | S05 | Has Text Message? | Decision | Cek apakah ada pesan teks |
| 6 | S06 | Keyword Detection | Process | Deteksi keyword dalam pesan |
| 7 | S07 | Match CREATE_WORD? | Decision | Cek keyword untuk Word |
| 8 | S08 | Match CREATE_EXCEL? | Decision | Cek keyword untuk Excel |
| 9 | S09 | Match CREATE_PPT? | Decision | Cek keyword untuk PPT |
| 10 | S10 | Match READ_FILE? | Decision | Cek keyword untuk baca file |
| 11 | S11 | Match CONVERT_PDF? | Decision | Cek keyword untuk konversi |
| 12 | S12 | Match LIST_FILES? | Decision | Cek keyword untuk list file |
| 13 | S13 | Default to CHAT | Process | Fallback ke mode chat |
| 14 | S14 | Execute Handler | Process | Jalankan handler sesuai intent |
| 15 | S15 | Generate Response | Process | Buat response untuk user |
| 16 | S16 | Send to UI | Process | Kirim hasil ke antarmuka |
| 17 | S17 | End | Terminal | Akhir proses |

## Keywords per Intent

| Intent | Keywords (Bahasa Indonesia) |
|--------|----------------------------|
| CREATE_WORD | buat word, bikin word, tulis dokumen, buat dokumen, buat surat, buat laporan |
| CREATE_EXCEL | buat excel, bikin excel, buat tabel, buat spreadsheet |
| CREATE_PPT | buat ppt, buat presentasi, bikin presentasi, buat slide |
| READ_FILE | baca, isi file, rangkum, summarize, jelaskan isi |
| CONVERT_PDF | convert, konversi, ubah ke pdf, jadikan pdf |
| LIST_FILES | list file, daftar file, file apa saja |
| CHAT | (default fallback) |

## Layout
**Rekomendasi:** Top-Down dengan multiple decision diamonds

```
                                    ┌─────────┐
                                    │  START  │
                                    └────┬────┘
                                         │
                                         ▼
                              ┌─────────────────────┐
                              │  Receive Message    │
                              │  from User          │
                              └──────────┬──────────┘
                                         │
                                         ▼
                                  ┌──────────────┐
                                 ╱                ╲
                                ╱  Has File       ╲
                               ╱   Upload?         ╲
                              ╱                     ╲
                             ╱───────────────────────╲
                            │ Yes                  No │
                            ▼                         │
                  ┌─────────────────┐                 │
                  │ Process Upload  │                 │
                  │ Save to Storage │                 │
                  └────────┬────────┘                 │
                           │                          │
                           └──────────┬───────────────┘
                                      │
                                      ▼
                                  ┌──────────────┐
                                 ╱                ╲
                                ╱  Has Text       ╲
                               ╱   Message?        ╲
                              ╱                     ╲
                             ╱───────────────────────╲
                            │ Yes                  No │
                            ▼                         │
                  ┌─────────────────┐                 │
                  │ Convert to      │                 │
                  │ Lowercase       │                 │
                  └────────┬────────┘                 │
                           │                          │
                           ▼                          │
        ┌──────────────────────────────────┐          │
        │      KEYWORD DETECTION CHAIN      │          │
        │  ┌────────────────────────────┐  │          │
        │  │ Check: "buat word"         │──┼──► Word Handler
        │  │ Check: "buat excel"        │──┼──► Excel Handler
        │  │ Check: "buat ppt"          │──┼──► PPT Handler
        │  │ Check: "baca"/"rangkum"    │──┼──► Read Handler
        │  │ Check: "convert"/"pdf"     │──┼──► Convert Handler
        │  │ Check: "list file"         │──┼──► List Handler
        │  │ Default                    │──┼──► Chat Handler
        │  └────────────────────────────┘  │          │
        └──────────────────────────────────┘          │
                           │                          │
                           ▼                          │
                  ┌─────────────────┐                 │
                  │ Execute Handler │◄────────────────┘
                  │ (Async)         │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │ Generate        │
                  │ Response        │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │ Send Response   │
                  │ to UI           │
                  └────────┬────────┘
                           │
                           ▼
                      ┌─────────┐
                      │   END   │
                      └─────────┘
```

## Mermaid Syntax

```mermaid
flowchart TD
    START([Start]) --> A1[Receive Message from User]
    A1 --> D1{Has File Upload?}
    
    D1 -->|Yes| A2[Process Upload<br/>Save to Storage]
    D1 -->|No| D2
    A2 --> D2
    
    D2{Has Text Message?}
    D2 -->|No| A3[Return Upload Success]
    D2 -->|Yes| A4[Convert to Lowercase]
    
    A4 --> KW[Keyword Detection]
    
    KW --> D3{"Contains<br/>'buat word' / 'bikin word'<br/>'tulis dokumen'?"}
    D3 -->|Yes| H1[Handler: CREATE_WORD]
    D3 -->|No| D4
    
    D4{"Contains<br/>'buat excel' / 'buat tabel'?"}
    D4 -->|Yes| H2[Handler: CREATE_EXCEL]
    D4 -->|No| D5
    
    D5{"Contains<br/>'buat ppt' / 'presentasi'?"}
    D5 -->|Yes| H3[Handler: CREATE_PPT]
    D5 -->|No| D6
    
    D6{"Contains<br/>'baca' / 'rangkum'?"}
    D6 -->|Yes| H4[Handler: READ_FILE]
    D6 -->|No| D7
    
    D7{"Contains<br/>'convert' / 'pdf'?"}
    D7 -->|Yes| H5[Handler: CONVERT_PDF]
    D7 -->|No| D8
    
    D8{"Contains<br/>'list file' / 'daftar'?"}
    D8 -->|Yes| H6[Handler: LIST_FILES]
    D8 -->|No| H7[Handler: CHAT<br/>Default Fallback]
    
    H1 --> EX[Execute Handler]
    H2 --> EX
    H3 --> EX
    H4 --> EX
    H5 --> EX
    H6 --> EX
    H7 --> EX
    A3 --> EX
    
    EX --> GEN[Generate Response]
    GEN --> SEND[Send Response to UI]
    SEND --> END([End])
```

---

# Lampiran: Ringkasan Diagram

| No | Diagram | Tujuan | Format Rekomendasi |
|----|---------|--------|-------------------|
| 1 | System Architecture Diagram | Menunjukkan komponen sistem dan hubungannya | draw.io, Visio |
| 2 | Use Case Diagram | Menidentifikasi aktor dan fungsionalitas sistem | draw.io, StarUML |
| 3 | Activity Diagram | Menggambarkan alur kerja utama | draw.io, Visio |
| 4 | Swimlane Diagram | Menunjukkan pembagian tanggung jawab | draw.io, Visio |
| 5 | Flowchart | Menjelaskan logika keputusan internal | draw.io, Visio |

---

# Catatan Keamanan Data (Data Sovereignty)

Seluruh sistem MENTARI dirancang dengan prinsip **kedaulatan data (data sovereignty)** sebagai berikut:

1. **Pemrosesan Lokal** - Semua operasi berjalan di mesin lokal tanpa koneksi ke layanan cloud
2. **LLM On-Premise** - Model AI (Qwen 2.5 Coder) berjalan melalui Ollama di localhost
3. **Penyimpanan Lokal** - Semua dokumen disimpan di filesystem lokal (USER_FILES/)
4. **Protokol Internal** - Komunikasi antar komponen menggunakan stdio dan localhost
5. **Tidak Ada Data Keluar** - Tidak ada data yang dikirim ke server eksternal

---

*Dokumen ini disusun untuk keperluan Laporan Kerja Praktek / Skripsi*
*Program Studi Teknik Informatika*
