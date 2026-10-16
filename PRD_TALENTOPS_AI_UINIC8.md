# PRODUCT REQUIREMENT DOCUMENT (PRD)

## Project Name: TalentOps AI
**Subtitle:** Autonomous Recruitment Web Ecosystem: From Resume Ingestion to Evidence-Based Hiring Decisions  
**Target Event:** Web Development Competition - UINIC 8.0 (2026)  
**Theme:** Engineering Interconnected Web Solutions to Empower Digital Ecosystems  
**Subtheme:** Artificial Intelligence (AI) & Teknologi Digital (SDG 8: Decent Work and Economic Growth & SDG 9: Industry, Innovation, and Infrastructure)  
**Document Version:** 1.0.0  
**Status:** Approved for Implementation  

---

## 1. EXECUTIVE SUMMARY & PRODUCT OVERVIEW

### 1.1 Problem Statement
Proses rekrutmen talenta teknik (*engineering hiring*) pada platform konvensional (Applicant Tracking System / ATS berbasis kata kunci) menghadapi tiga kegagalan struktural:
1. **Ketidakefisienan Operasional:** Evaluasi manual satu berkas pelamar memerlukan rata-rata 8-10 menit. Volume ratusan kandidat memicu *decision fatigue* bagi tim Human Resources (HR).
2. **Kerapuhan Validasi Klaim (*Keyword Stuffing & Fraud*):** ATS konvensional hanya mencocokkan kemunculan teks, rentan dimanipulasi kandidat tanpa verifikasi bukti proyek nyata, serta sering melewatkan anomali kronologi karier (*overlapping dates*, klaim gelar fiktif).
3. **Ketertutupan dan Bias Algoritmik:** Banyak solusi berbasis AI bertindak sebagai "black box" atau sekadar pembungkus API (*wrapper* LLM) yang rawan halusinasi skor tanpa rujukan bukti konkret (*evidence chain*), serta memicu diskriminasi gender/usia/institusi.

### 1.2 Proposed Solution
**TalentOps AI** adalah ekosistem aplikasi web rekrutmen otonom yang mengintegrasikan arsitektur *interconnected web*:
- Antarmuka berbasis Single Page Application (SPA) responsif dengan visualisasi graf pembuktian (*Evidence Graph*).
- Mesin backend deterministik (*FastAPI*) dengan orkestrasi penalaran bertahap (*multi-turn state machine*).
- Basis data relasional *MariaDB* 10 tabel terenkapsulasi penuh (*ACID compliant*) untuk pencatatan jejak audit (*audit trail*) dan konsistensi data reservasi wawancara.
- Gerbang kecerdasan buatan terstruktur (*Strict JSON Schema*) yang memisahkan logika penalaran probabilitis dari kalkulasi skor deterministik.
- Fitur pemulihan mandiri (*Bounded Self-Healing Circuit*) saat terjadi bentrok alokasi sumber daya kalender (HTTP 409).

### 1.3 Key Value Proposition
- **Efisiensi Waktu Review:** Memangkas durasi evaluasi per kandidat dari 480 detik menjadi < 15 detik (penurunan 96.8%).
- **Transparansi Berbasis Bukti:** Setiap poin skor ditautkan secara langsung ke sumber kutipan resume atau tautan portofolio (Candidate Evidence Graph).
- **Integritas & Kepatuhan:** Fitur sanitasi identitas pribadi (*PII Sanitization*) dan pengawasan keadilan (*Fairness Guardian*) mencegah bias seleksi.
- **Keandalan Sistem Tinggi (*High Reliability*):** Mekanisme *concurrency locking* dan *self-healing fallback* menjamin ketiadaan reservasi jadwal ganda (*double-booking*).

---

## 2. USER PERSONA & STAKEHOLDER ANALYSIS

### 2.1 Technical Recruiter / Talent Acquisition (TA) Specialist
- **Tujuan:** Menyaring puluhan hingga ratusan pelamar kerja secara akurat dan objektif tanpa kelelahan kognitif.
- **Pain Point:** Kesulitan memvalidasi klaim teknis yang rumit, waktu habis untuk penjadwalan manual, dan risiko subjektivitas.
- **Kebutuhan Fitur:** Dashboard terpadu, filter status kandidat otomatis, ekspor draf undangan, dan transparansi skor.

### 2.2 Engineering Hiring Manager / Tech Lead
- **Tujuan:** Mendapatkan kandidat yang memiliki kapabilitas teknis valid sesuai kebutuhan proyek tim.
- **Pain Point:** Menerima kandidat lolos seleksi berkas yang ternyata tidak mampu menjawab persoalan teknis mendasar saat wawancara kerja.
- **Kebutuhan Fitur:** Matriks kesenjangan kompetensi (*skill gap analysis*), generator soal asesmen adaptif berbasis riwayat proyek kandidat, dan visualisasi bukti pengalaman.

### 2.3 HR Compliance & Audit Officer
- **Tujuan:** Memastikan prosedur rekrutmen terbebas dari bias personal, diskriminasi SARA, serta memenuhi regulasi perlindungan data pribadi (UU PDP).
- **Pain Point:** Kurangnya jejak audit saat keputusan penolakan atau penerimaan kandidat dipertanyakan.
- **Kebutuhan Fitur:** Rekaman jejak audit langkah agen (*agent execution audit logs*), tabel histori manipulasi kebijakan (*what-if simulation logs*), dan mode anonimitas kandidat (*masked identifier*).

---

## 3. ARSITEKTUR TEKNIS & INTERKONEKSI SISTEM

Sistem dibangun dengan arsitektur 3-Tier terdistribusi yang mengedepankan prinsip *interoperability* dan *low-latency data streaming*:

```
+---------------------------------------------------------------------------------------+
|                                    PRESENTATION TIER                                  |
|   React 18 + Vite 5 + Tailwind CSS + Lucide Icons + Recharts (Deploy: Vercel CDN)    |
|   - Dashboard Rekrutmen Interaktif & Drag-and-Drop Ingestion                          |
|   - Real-time Execution Feed (Server-Sent Events / SSE Client)                        |
|   - Candidate Evidence Interactive Graph Modal                                        |
|   - "What-If?" Hiring Policy Simulator Visual Matrix                                  |
+---------------------------------------------------------------------------------------+
                                  |                     ^
             HTTPS REST Endpoints |                     | Server-Sent Events (SSE Stream)
             (JSON Payloads)      v                     |
+---------------------------------------------------------------------------------------+
|                               APPLICATION & ENGINE TIER                               |
|   Python 3.12 + FastAPI Core + Pydantic v2 + Uvicorn Async Worker                     |
|   - State Machine Engine: Understand -> Sanitize -> Investigate -> Score -> Schedule  |
|   - Fairness Guardian & PII Redaction Parser                                          |
|   - Deterministic 4-Dimensional Scoring Calculator                                    |
|   - Bounded Self-Healing Controller (HTTP 409 Conflict Auto-Retry & Fallback Slot)    |
|   - Host: Linux VPS, Nginx Reverse Proxy, Let's Encrypt TLS/SSL                       |
+---------------------------------------------------------------------------------------+
             |                                                  |
             | aiomysql (Async Pool)                            | Strict JSON RPC / Tool Calls
             v                                                  v
+----------------------------------------+     +----------------------------------------+
|             DATA PERSISTENCE           |     |          AI REASONING GATEWAY          |
|   MariaDB 10.x Enterprise RDBMS        |     |   9Router / Custom Local Gateway       |
|   - 10 Relational Tables (ACID)        |     |   Model: ag/gemini-3.7-flash           |
|   - Optimistic Locking (versioning)    |     |   - Zero-shot claim verification       |
|   - Complete Audit Trail Logs          |     |   - Adaptive scenario synthesis        |
+----------------------------------------+     +----------------------------------------+
```

---

## 4. SKEMA LENGKAP BASIS DATA MARIADB (10 TABEL RELASIONAL)

Struktur basis data dirancang dalam format relasional normalisasi ketiga (3NF) guna menjamin integritas referensial dan skalabilitas transaksi:

```sql
-- 1. Definisi Lowongan Kerja & Job Intelligence
CREATE TABLE job_descriptions (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(150) NOT NULL,
    department VARCHAR(100) NOT NULL,
    raw_text LONGTEXT NOT NULL,
    core_competencies JSON NOT NULL,
    flagged_ambiguities JSON NULL,
    weight_config JSON NOT NULL,
    status ENUM('active', 'closed') DEFAULT 'active',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- 2. Data Master Kandidat (4-Dimensional Evaluation Profile)
CREATE TABLE candidates (
    id INT AUTO_INCREMENT PRIMARY KEY,
    job_id INT NOT NULL,
    name VARCHAR(150) NOT NULL,
    email VARCHAR(150) NOT NULL,
    phone VARCHAR(50),
    masked_identifier VARCHAR(64) NOT NULL,
    resume_file_path VARCHAR(255) NOT NULL,
    overall_score DECIMAL(5,2) DEFAULT 0.00,
    confidence_level ENUM('HIGH', 'MEDIUM', 'LOW') DEFAULT 'HIGH',
    unresolved_risk ENUM('LOW', 'MEDIUM', 'HIGH') DEFAULT 'LOW',
    recommendation ENUM('STRONG_MATCH', 'REVIEW_REQUIRED', 'WEAK_FIT') DEFAULT 'REVIEW_REQUIRED',
    status ENUM('analyzing', 'waiting_approval', 'approved', 'rejected') DEFAULT 'analyzing',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_candidate_job (job_id),
    INDEX idx_candidate_recommendation (recommendation),
    FOREIGN KEY (job_id) REFERENCES job_descriptions(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- 3. Bukti Rekomendasi (Evidence-Based Traceability & Graph)
CREATE TABLE candidate_evidence (
    id INT AUTO_INCREMENT PRIMARY KEY,
    candidate_id INT NOT NULL,
    dimension ENUM('SKILL', 'EXPERIENCE', 'EDUCATION', 'PORTFOLIO') NOT NULL,
    claim_text TEXT NOT NULL,
    evidence_source TEXT NOT NULL,
    score_contribution DECIMAL(5,2) NOT NULL,
    confidence DECIMAL(3,2) NOT NULL,
    INDEX idx_evidence_candidate (candidate_id),
    FOREIGN KEY (candidate_id) REFERENCES candidates(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- 4. Anomali & Risiko Investigasi Karier
CREATE TABLE candidate_anomalies (
    id INT AUTO_INCREMENT PRIMARY KEY,
    candidate_id INT NOT NULL,
    anomaly_type VARCHAR(100) NOT NULL,
    severity ENUM('LOW', 'MEDIUM', 'HIGH') NOT NULL,
    description TEXT NOT NULL,
    suggested_verification TEXT NOT NULL,
    INDEX idx_anomalies_candidate (candidate_id),
    FOREIGN KEY (candidate_id) REFERENCES candidates(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- 5. Soal Asesmen Teknis Adaptif (Gap-based Challenge)
CREATE TABLE candidate_assessments (
    id INT AUTO_INCREMENT PRIMARY KEY,
    candidate_id INT NOT NULL,
    target_skill VARCHAR(100) NOT NULL,
    difficulty_level ENUM('L1_FUNDAMENTAL', 'L2_SYSTEM_DESIGN', 'L3_EDGE_CASE') NOT NULL,
    scenario_question TEXT NOT NULL,
    evaluation_criteria TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_assessments_candidate (candidate_id),
    FOREIGN KEY (candidate_id) REFERENCES candidates(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- 6. Slot Jadwal Wawancara (Atomic Lock Calendar Engine)
CREATE TABLE interview_slots (
    id INT AUTO_INCREMENT PRIMARY KEY,
    candidate_id INT NULL,
    interviewer_name VARCHAR(100) NOT NULL,
    scheduled_time DATETIME NOT NULL,
    status ENUM('available', 'tentative', 'booked') DEFAULT 'available',
    meeting_link VARCHAR(255) NULL,
    lock_version INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_slots_status (status),
    FOREIGN KEY (candidate_id) REFERENCES candidates(id) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- 7. Audit Trail Langkah Siklus Agen
CREATE TABLE agent_steps (
    id INT AUTO_INCREMENT PRIMARY KEY,
    run_id VARCHAR(36) NOT NULL,
    candidate_id INT NOT NULL,
    step_name VARCHAR(100) NOT NULL,
    status ENUM('started', 'completed', 'failed', 'recovered') NOT NULL,
    payload_input JSON NULL,
    payload_output JSON NULL,
    error_message TEXT NULL,
    duration_ms INT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_run_candidate (candidate_id),
    INDEX idx_run_step (run_id),
    FOREIGN KEY (candidate_id) REFERENCES candidates(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- 8. Log Eksekusi Pemanggilan Alat (Tool Calling & API Log)
CREATE TABLE tool_call_logs (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    run_id VARCHAR(36) NOT NULL,
    tool_name VARCHAR(100) NOT NULL,
    input_params JSON NULL,
    output_result JSON NULL,
    status ENUM('SUCCESS', 'FAILED', 'RECOVERED') NOT NULL,
    execution_time_ms INT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_tool_run (run_id),
    INDEX idx_tool_name (tool_name)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- 9. Persetujuan Manusia (Human-In-The-Loop / HITL Gatekeeper)
CREATE TABLE human_reviews (
    id INT AUTO_INCREMENT PRIMARY KEY,
    candidate_id INT NOT NULL,
    reviewer_name VARCHAR(100) NOT NULL,
    decision ENUM('APPROVED_DISPATCH', 'REQUEST_INFO', 'OVERRIDE_REJECT') NOT NULL,
    notes TEXT NULL,
    reviewed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_review_candidate (candidate_id),
    FOREIGN KEY (candidate_id) REFERENCES candidates(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- 10. Log Simulasi Kebijakan Rekrutmen (What-If Simulator)
CREATE TABLE simulation_logs (
    id INT AUTO_INCREMENT PRIMARY KEY,
    job_id INT NOT NULL,
    scenario_name VARCHAR(150) NOT NULL,
    modified_parameters JSON NOT NULL,
    impact_metrics JSON NOT NULL,
    simulated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_sim_job (job_id),
    FOREIGN KEY (job_id) REFERENCES job_descriptions(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

## 5. DETAIL SPESIFIKASI FITUR FUNGSIONAL (FUNCTIONAL REQUIREMENTS)

### FR-1: Intelligent Job Description Analyzer & Bias Auditor
- **Deskripsi:** Sistem menganalisis teks kriteria lowongan kerja yang diunggah pengguna.
- **Input:** Teks deskripsi pekerjaan mentah (raw job posting).
- **Proses:** Ekstraksi kompetensi inti (*hard skill*, *soft skill*, domain proyek) dan audit kata kunci ambigu atau bias (contoh: batasan usia tidak relevan, istilah tidak terukur).
- **Output:** JSON spesifikasi lowongan tersimpan pada tabel `job_descriptions` beserta bobot evaluasi 4 dimensi.

### FR-2: Resume Ingestion, Text Extraction & PII Sanitizer
- **Deskripsi:** Pengunggahan berkas lamaran dengan proteksi privasi otomatis.
- **Input:** Dokumen resume (PDF/DOCX) via antarmuka drag-and-drop.
- **Proses:** Ekstraksi teks in-memory, identifikasi PII (alamat fisik, foto, status pernikahan, usia, nama lengkap) dan pembuatan kode anonim `masked_identifier` (misal: `CAND-TECH-9021`).
- **Output:** Record baru pada tabel `candidates` dengan status `analyzing`.

### FR-3: Deep Evidence Extraction & Candidate Evidence Graph
- **Deskripsi:** Mengidentifikasi keabsahan klaim pengalaman pada berkas pelamar.
- **Input:** Teks resume teranonimisasi dan kompetensi lowongan.
- **Proses:**
  - Menemukan klaim kandidat beserta lokasi halaman berkas atau tautan proyek publik (GitHub/URL).
  - Mengkalkulasi kontribusi skor pada 4 dimensi: Skill Teknis (40%), Riwayat Pengalaman (30%), Portofolio & Proyek Nyata (20%), Latar Belakang Pendidikan/Sertifikasi (10%).
  - Formula kalkulasi bersifat deterministik:
    $$\text{Overall Score} = \sum (\text{Dimension Score} \times \text{Weight})$$
- **Output:** Rekaman baris bukti pada tabel `candidate_evidence` dan pemetaan simpul graf (*graph nodes & edges*) pada antarmuka web.

### FR-4: Career Timeline Auditor & Anomaly Detection
- **Deskripsi:** Mendeteksi inkonsistensi waktu kerja atau klaim berlebihan (*title inflation*).
- **Input:** Riwayat urutan kerja kandidat.
- **Proses:** Pemeriksaan tumpang tindih tanggal (*overlapping employment dates* > 3 bulan di posisi *full-time* bersamaan) dan lompatan peran fiktif tanpa rekam jejak.
- **Output:** Klasifikasi risiko (`LOW`, `MEDIUM`, `HIGH`) pada tabel `candidate_anomalies`.

### FR-5: Gap-based Adaptive Technical Challenge Generator
- **Deskripsi:** Membuat soal studi kasus berbasis kesenjangan kompetensi kandidat.
- **Input:** Dimensi kompetensi kandidat yang memiliki nilai keyakinan (*confidence*) di bawah ambang batas batas 70%.
- **Proses:** Sintesis 1-2 pertanyaan pemecahan masalah teknis arsitektur nyata (*system design / concurrency scenario*) untuk diujikan pada sesi wawancara.
- **Output:** Rekaman pada tabel `candidate_assessments`.

### FR-6: Atomic Interview Scheduler with Bounded Self-Healing
- **Deskripsi:** Pemesanan slot kalender wawancara otomatis dengan mitigasi bentrok konkurensi.
- **Proses:**
  - Mengambil slot waktu `available` dari tabel `interview_slots`.
  - Melakukan locking atomik berbasis *optimistic concurrency control* (`lock_version = lock_version + 1`).
  - **Self-Healing Branch:** Jika saat transaksi berlangsung slot telah diambil sesi lain (`HTTP 409 Conflict`), sistem secara otomatis menjalankan *circuit recovery*:
    1. Menangkap status konflik tanpa melempar kegagalan ke pengguna.
    2. Mencari slot ketersediaan berikutnya secara otomatis.
    3. Mengunci slot alternatif dan mencatat status `RECOVERED` pada tabel `agent_steps` dan `tool_call_logs`.
- **Output:** Slot wawancara berstatus `tentative` siap diverifikasi oleh HR.

### FR-7: Real-Time Execution Trace Stream (Server-Sent Events)
- **Deskripsi:** Menampilkan transparansi penalaran dan eksekusi sistem secara langsung kepada pengguna di antarmuka web.
- **Teknologi:** Server-Sent Events (SSE) dengan transmisi JSON *chunk-by-chunk* via endpoint `/api/candidates/{id}/trace-stream`.
- **Output:** Terminal visual mini pada antarmuka web menampilkan tahapan kerja sistem, durasi eksekusi per milidetik, dan status alat.

### FR-8: Human-In-The-Loop (HITL) Gatekeeper
- **Deskripsi:** Menjamin kendali keputusan akhir tetap berada di tangan manusia.
- **Proses:**
  - Kandidat dengan status `STRONG_MATCH` dialokasikan ke antrean persetujuan cepat (*fast-track*).
  - Kandidat dengan status `REVIEW_REQUIRED` (memiliki anomali kronologi karier) ditandai dengan lencana peringatan kuning untuk ditinjau manual.
  - Reviewer menekan tombol *Approve Dispatch*, *Request Clarification*, atau *Override Reject*.
- **Output:** Record tercatat pada tabel `human_reviews`.

### FR-9: "What-If?" Hiring Policy Simulator
- **Deskripsi:** Simulasi dampak pelonggaran atau pengetatan kebijakan kualifikasi terhadap *talent pool*.
- **Parameter Uji:** Toggle syarat minimal gelar S1, penyesuaian bobot pengalaman kerja, pengetatan ambang batas skor minimum.
- **Output:** Grafik perbandingan seketika (*instant impact matrix*): persentase kandidat yang lolos kualifikasi, estimasi beban kerja review tim HR, dan rata-rata skor teknis kandidat. Disimpan pada tabel `simulation_logs`.

---

## 6. NON-FUNCTIONAL REQUIREMENTS (NFR)

### 6.1 Performance & Latency
- Waktu respon pemanggilan API endpoint non-LLM (CRUD MariaDB) harus $\le 120\text{ ms}$ pada persentil 95 (P95).
- Alur komprehensif evaluasi berkas resume (ingestion, sanitasi, investigasi bukti, kalkulasi skor, pembuatan asesmen adaptif) selesai dalam waktu maksimal 15 detik per berkas.
- Sambungan Server-Sent Events (SSE) memiliki latensi pengiriman pesan $< 50\text{ ms}$ sejak aksi backend dicatat.

### 6.2 Security & Compliance (OWASP Web & Data Protection)
- **Sanitasi Input:** Seluruh payload JSON divalidasi ketat menggunakan skema Pydantic v2 untuk mencegah serangan *SQL Injection* dan *Cross-Site Scripting* (XSS).
- **Perlindungan Berkas:** Berkas PDF/DOCX yang diunggah divalidasi tipe MIME dan batasan ukuran maksimal 5 MB.
- **Data Privacy:** Data identitas sensitif (PII) tidak dikirimkan ke log sistem maupun ke prompt LLM eksternal, sesuai mandat prinsip minimalisasi data UU No. 27 Tahun 2022 (UU PDP).
- **Database Security:** Akses MariaDB menggunakan akun terikat dengan pembatasan hak akses (*least privilege principle*), diproteksi firewall jaringan internal.

### 6.3 Reliability & Availability
- Arsitektur backend FastAPI berjalan di balik Nginx Reverse Proxy dengan konfigurasi *keep-alive connections* dan *graceful reload*.
- Mengimplementasikan penanganan kegagalan sementara (*transient error handler*) dengan pola *exponential backoff retry* (maksimal 3 kali percobaan).

### 6.4 Usability & Accessibility
- Antarmuka web memenuhi standar kontras warna WCAG 2.1 Level AA.
- Desain tata letak responsif penuh (*fluid grid*) mendukung resolusi desktop (1920x1080), laptop (1366x768), dan tablet (768x1024).

---

## 7. ARSITEKTUR REST API SPESIFIKASI LENGKAP

| Method | Endpoint | Deskripsi | Input Utama | Output Utama |
|---|---|---|---|---|
| `POST` | `/api/jobs` | Membuat dan menganalisis lowongan kerja | `title, department, raw_text, weight_config` | ID lowongan, kompetensi inti JSON |
| `GET` | `/api/jobs/{id}` | Mengambil detail spesifikasi lowongan | `id` | Metadata lowongan & bobot |
| `POST` | `/api/candidates/upload` | Ingestion berkas resume & trigger agen | `job_id, file (multipart/form-data)` | `candidate_id, masked_identifier, status` |
| `GET` | `/api/candidates` | Mengambil daftar kandidat per lowongan | `job_id, recommendation, status` | Array list ringkasan profil kandidat |
| `GET` | `/api/candidates/{id}` | Mengambil data mendalam kandidat | `id` | Profil skor 4D, anomali, & asesmen |
| `GET` | `/api/candidates/{id}/evidence-graph` | Mengambil struktur simpul graf pembuktian | `id` | Array nodes & edges (recharts/vis) |
| `GET` | `/api/candidates/{id}/trace-stream` | Aliran SSE eksekusi langkah agen | `id` | Data stream: `step_name, status, duration_ms` |
| `POST` | `/api/candidates/{id}/review` | Aksi verifikasi gerbang manusia (HITL) | `reviewer_name, decision, notes` | Status persetujuan terbaru |
| `POST` | `/api/slots/reserve` | Mengunci slot kalender secara atomik | `candidate_id, slot_id` | Status reservasi / self-healing result |
| `POST` | `/api/simulations/run` | Menjalankan simulasi kebijakan rekrutmen | `job_id, scenario_name, parameters` | Matriks dampak & perbandingan metrik |

---

## 8. STRUKTUR NAVIGASI & DESAIN ANTARMUKA WEB

```
[PORTAL UTAMA TALENTOPS AI]
|
+--- 1. Top Navigation Bar: Identitas Produk, Navigasi Halaman, Indikator Sistem VPS, Profil Pengguna
|
+--- 2. Job Intelligence & Overview View:
|    +-- Statistik Ringkas: Total Kandidat, Lolos Otomatis, Perlu Review, Terarsip
|    +-- Komponen Upload Drag-and-Drop Resume (Batch Ingestion)
|
+--- 3. Candidate Triage Board (Split-View Layout):
|    +-- Panel Kiri: Daftar Kandidat (Kartu Ringkas: Masked ID, Skor 4D, Badges Status, Confidence)
|    +-- Panel Kanan: Dynamic Candidate Dossier:
|         * Tab 1: 4-Dimensional Score Breakdown (Radar / Bar Chart)
|         * Tab 2: Candidate Evidence Graph (Peta interaktif klaim vs rujukan bukti)
|         * Tab 3: Career Timeline & Anomaly Detection (Visualisasi urutan tahun kerja)
|         * Tab 4: Adaptive Assessment Preview (Soal skenario teknis gap-based)
|         * Tab 5: Live Execution Trace (Feed rekaman langkah agen & tool calling)
|         * Action Bar: Tombol Keputusan HITL (Approve, Request Clarify, Reject)
|
+--- 4. "What-If?" Simulation Studio:
|    +-- Kontrol Slider: Ambang Batas Skor, Persyaratan Jenjang Pendidikan, Bobot Portofolio
|    +-- Visual Matriks Dampak: Delta Lolos (+%), Delta Beban HR (Jam), Perubahan Rata-rata Kualitas
|
+--- 5. Audit & Compliance Explorer:
     +-- Tabel Interaktif 10 Entitas Relasional: Filter Run ID, Tool Logs, dan Riwayat Human Reviews
```

---

## 9. RENCANA PENGUJIAN & METRIK KEBERHASILAN (TESTING & METRICS)

### 9.1 Skenario Pengujian Fungsional (3 Kasus Uji Utama)
1. **Kasus 1: Kandidat Valid (Budi Santoso - Strong Match)**
   - *Kriteria Masukan:* Pengalaman backend 5 tahun terverifikasi, tautan repo GitHub valid, tanpa anomali tanggal.
   - *Hasil yang Diharapkan:* Skor $\ge 88.0$, keyakinan `HIGH`, rekomendasi `STRONG_MATCH`, alokasi slot wawancara tentative, status menunggu persetujuan cepat HR.
2. **Kasus 2: Kandidat Anomali (Reza Pratama - Review Required)**
   - *Kriteria Masukan:* Klaim 5 tahun pengalaman, tetapi terdeteksi tumpang tindih tanggal kerja 17 bulan pada dua instansi *full-time*.
   - *Hasil yang Diharapkan:* Anomali terdeteksi berstatus `MEDIUM`, sistem menghasilkan soal asesmen adaptif terkait konkurensi, rekomendasi `REVIEW_REQUIRED` (HITL gatekeeper diaktifkan).
3. **Kasus 3: Kandidat Non-Relevan (Citra Lestari - Weak Fit)**
   - *Kriteria Masukan:* Resume desain grafis untuk posisi backend engineer.
   - *Hasil yang Diharapkan:* Skor $< 30.0$, ketiadaan bukti kompetensi teknis, rekomendasi `WEAK_FIT`, diarahkan ke arsip.
4. **Kasus 4: Simulasi Bentrok Jadwal (Self-Healing Recovery)**
   - *Kriteria Masukan:* Slot kalender ID 101 sengaja dikunci proses lain sebelum agen melakukan konfirmasi.
   - *Hasil yang Diharapkan:* Engine menangkap respon HTTP 409, beralih ke slot alternatif ID 102 tanpa eror antarmuka, status tercatat `RECOVERED` di tabel `agent_steps`.

### 9.2 Metrik Kinerja Produk (Target Indikator Lomba)
- **Tingkat Akurasi Deteksi Anomali:** $\ge 90\%$ kecocokan deteksi terhadap dataset resume beranomali buatan.
- **Waktu Pemulihan Mandiri (*Self-Healing Recovery Time*):** $< 350\text{ ms}$ untuk menangani bentrok HTTP 409 dan mengunci slot alternatif.
- **Integritas Transaksi:** 0 kasus *race condition* atau *double-booking* kalender pada pengujian konkurensi 50 permintaan simultan.
- **Skor Keterpakaian Sistem (System Usability Scale / SUS):** Target skor evaluasi $\ge 82.5$ (kategori *Excellent*).

---

## 10. ALOKASI TAHAP IMPLEMENTASI & ROADMAP LOMBA UINIC 8.0

| Fase | Target Luaran | Durasi / Batas Waktu |
|---|---|---|
| **Fase 1: Database & Backend Core** | Skema 10 tabel MariaDB terpasang, integrasi FastAPI dengan aiomysql, endpoint CRUD dasar | Hari ke-1 s.d. 3 |
| **Fase 2: AI State Machine & Gateway** | Parser resume in-memory, PII Sanitizer, prompt strict JSON schema, logika 4D scoring deterministik | Hari ke-4 s.d. 6 |
| **Fase 3: Self-Healing & Calendar Locking** | Implementasi optimistic locking, simulasi penanganan HTTP 409 conflict, SSE stream endpoint | Hari ke-7 s.d. 8 |
| **Fase 4: Frontend Web SPA Development** | Antarmuka React 18, dashboard triage, visualisasi modal Evidence Graph, What-If simulator | Hari ke-9 s.d. 12 |
| **Fase 5: Integrasi & Deploy Publik** | Deployment backend di Ubuntu VPS + HTTPS Nginx, deploy frontend di Vercel, pengujian end-to-end | Hari ke-13 s.d. 14 |
| **Fase 6: Penyusunan Dokumen Proposal** | Penulisan proposal lengkap 30 halaman format PDF sesuai Bab II Poin J Guidebook UINIC 8.0 | Menjelang 30 Oktober 2026 |
