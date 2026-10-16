# Alur Kerja Sistem TalentOps AI
**Autonomous Recruitment Operating System**

Dokumen arsitektur operasional, State Machine, klasifikasi self-healing, dan visualisasi alur end-to-end untuk **GTNIC 2026**.

---

## 1. HIGH-LEVEL ARCHITECTURE & SERVICE SEPARATION

Sistem memisahkan **Candidate Execution Pipeline** (aliran operasional) dan **Policy Simulator** (layanan intelijen paralel):

```
+-----------------------------------------------------------------------------------------+
|                                    TALENTOPS PLATFORM                                   |
+-----------------------------------------------------------------------------------------+
                                             |
                     +-----------------------+-----------------------+
                     |                                               |
                     v                                               v
+-----------------------------------------+     +-----------------------------------------+
|    CANDIDATE PROCESSING PIPELINE        |     |   PARALLEL POLICY SIMULATOR (WHAT-IF)   |
|  - Ingestion -> Sanitize -> Investigate |     |  - Baseline candidate dataset cache     |
|  - Fusion -> 4D Scoring -> Adaptive     |     |  - Mutation: Remove degree / alter exp  |
|  - Self-Healing Scheduler -> HITL       |     |  - In-memory recalculation              |
|  - Granular Audit Trail (10 DB Tables)  |     |  - Workload & quality impact analysis   |
+-----------------------------------------+     +-----------------------------------------+
                     \                                               /
                      \                                             /
                       v                                           v
+-----------------------------------------------------------------------------------------+
|                    HR GOVERNANCE DASHBOARD (REACT 18 + VITE + SSE)                      |
|  - Live Execution Trace     - Evidence Graph     - HITL Authorization Gate              |
+-----------------------------------------------------------------------------------------+
```

---

## 2. DETAILED CANDIDATE PIPELINE (END-TO-END)

```
[HR Recruiter Input: Job Description Text]
                     |
                     v
             [1. JOB ANALYZER]
                     |
       +-------------+-------------+
       | (Ambiguity Detected)      | (Clear Criteria)
       v                           v
  [LOG_WARNING]              [JOB PROFILE]
  - Non-job related flag     - Core Competencies
  - Measurable suggestion    - Weight Configuration
       |                           |
       +-------------+-------------+
                     |
                     v
             [2. CV INGESTION] (Batch PDF)
                     |
                     v
             [3. PII SANITIZER (FAIRNESS GUARDIAN)]
             - Identifikasi & eksklusi data personal non-job-related
             - Generate masked_identifier (e.g. CAND-8F92)
                     |
                     v
             [4. INVESTIGATION & EVIDENCE LAYER]
                     |
       +-------------+-------------+
       |                           |
       v                           v
  [Claim Verification]        [Timeline Audit]
  - Ekstrak klaim teknis      - Audit tanggal riwayat kerja
  - Validasi bukti nyata      - Deteksi tumpang tindih (overlap)
  - Cek referensi repo/proyek - Cek lonjakan jabatan janggal
       |                           |
       +-------------+-------------+
                     |
                     v
             [5. EVIDENCE FUSION]
             - Gabungkan hasil menjadi Candidate Evidence Profile
             - Petakan klaim terbukti vs klaim tanpa bukti
                     |
                     v
             [6. 4-DIMENSIONAL SCORING ENGINE]
             (Deterministic calculation - no hallucination)
             ├── Fit Score: 0 - 100
             ├── Confidence Level: HIGH / MEDIUM / LOW
             ├── Unresolved Risk: LOW / MEDIUM / HIGH
             └── Human Decision: PENDING
                     |
       +-------------+-------------+
       |                           |
       v                           v
  [STRONG / REVIEW]            [WEAK FIT]
       |                           |
       v                           v
  [7. ADAPTIVE ASSESSMENT]     [DRAFT REJECT FEEDBACK]
  - Evidence-seeking challenge - Alasan kekurangan bukti relevan
  - Menguji unverified claim   - Status: PENDING HR
       |                           |
       v                           |
  [8. ACTION PLANNER & EXECUTION]  |
  - Lock tentative interview slot  |
  - Generate draft email dispatch  |
       |                           |
       v                           |
  (Error handling via Self-Healing)|
       |                           |
       +-------------+-------------+
                     |
                     v
       [9. HUMAN-IN-THE-LOOP (HITL) APPROVAL]
       - HR inspect "Why This Candidate?" & Evidence Graph
       - Action: [Approve & Dispatch] / [Clarify] / [Reject]
                     |
                     v
       [10. AUDIT TRAIL PERSISTENCE]
       - Rekam hasil ke 10 tabel MariaDB
```

---

## 3. STATE MACHINE TRANSITIONS (AUTONOMOUS STATE MACHINE)

Istilah teknis resmi: **Custom Agentic State Machine with ReAct-inspired Tool Orchestration**.

```
                  [IDLE]
                    |
                    | Upload Trigger
                    v
               [UNDERSTAND]
                    |
      +-------------+-------------+
      | (Ambiguity)               | (Valid)
      v                           |
  [LOG_WARNING]                   |
      |                           |
      +------------->+<-----------+
                     |
                     v
                 [SANITIZE]
                     |
                     v
                [INVESTIGATE]
                ├── tool_extract_claims()
                └── tool_audit_timeline()
                     |
                     v
             [EVIDENCE_FUSION]
                     |
                     v
                 [EVALUATE]
                     |
      +--------------+--------------+
      | (Fit >= 75)  | (Gap/Overlap)| (Low Relevance)
      v              v              v
  [STRONG_MATCH] [REVIEW_REQ]   [WEAK_FIT]
      |              |              |
      v              v              |
  [GEN_ADAPTIVE_CHALLENGE]          |
      |              |              |
      v              v              v
  [PLAN_ACTION]  [PLAN_INQUIRY] [PLAN_ARCHIVE]
      |              |              |
      v              |              |
  [EXECUTE_TOOL]     |              |
      |              |              |
      +-------+------+              |
              |                     |
     (Result Status)                |
      ├── SUCCESS ----------------->|
      └── FAILED                    |
             |                      |
             v                      |
      [CLASSIFY_ERROR]              |
      ├── Transient -> Retry <= 3 ->|
      ├── Conflict  -> Alt Strategy>|
      └── Permanent -> Escalate --->|
                                    |
                                    v
                         [WAITING_HUMAN_APPROVAL]
                                    |
                      +-------------+-------------+
                      |                           |
               (HR Authorized)             (HR Rejected)
                      |                           |
                      v                           v
              [DISPATCH_ACTION]               [ARCHIVED]
                      |                           |
                      +-------------+-------------+
                                    |
                                    v
                              [AUDIT_LOGGED]
```

---

## 4. BOUNDED SELF-HEALING CIRCUIT (KLASIFIKASI KEGAGALAN)

Agen tidak melakukan blind retry tanpa batas. Kegagalan diklasifikasikan ke dalam 3 strategi:

```
               [TOOL EXECUTION FAILURE]
                          |
                          v
               [ERROR CLASSIFIER ENGINE]
                          |
      +-------------------+-------------------+
      |                   |                   |
      v                   v                   v
[TRANSIENT ERROR]  [RESOURCE CONFLICT]  [PERMANENT ERROR]
- Contoh:           - Contoh:            - Contoh:
  Network timeout,    HTTP 409 Slot        HTTP 401 Unauthorized,
  DB deadlock         sudah di-lock        Format binary rusak
      |                   |                   |
      v                   v                   v
[EXPONENTIAL       [ALTERNATIVE         [IMMEDIATE
 BACKOFF]           STRATEGY]            ESCALATION]
- Max 3x retry     - Query slot         - Hentikan otomatisasi
- Delay: 1s, 2s, 4s  berikutnya di DB   - Tandai sesi:
      |            - Re-attempt lock      NEEDS_HUMAN_INTERVENTION
      v                   |             - Notifikasi HR
 (Success / Fail)         v                   |
      |            (Status: RECOVERED)        v
      +-------------------+------------> [AUDIT TRAIL]
```

---

## 5. SEQUENCE DIAGRAM: INVESTIGASI & SELF-HEALING DEMO

```
User (HR)          Frontend UI          FastAPI Engine         9Router (LLM)         MariaDB
   |                    |                     |                      |                  |
   |--- Upload CV ----->|                     |                      |                  |
   |                    |--- POST /analyze -->|                      |                  |
   |                    |                     |--- Sanitize PII ----------------------->| (Store PII Vault)
   |                    |                     |                      |                  |
   |                    |                     |--- Extract Claims -->|                  |
   |                    |                     |<-- Claims JSON ------|                  |
   |                    |                     |                                         |
   |                    |                     |--- Audit Timeline (Detect Overlap) ---->|
   |                    |                     |<-- Overlap Flagged (PT A vs PT B) ------|
   |                    |                     |                                         |
   |                    |                     |--- Evidence Fusion & 4D Scoring ------->| (Save candidate_evidence)
   |                    |                     |                                         |
   |                    |                     |--- Gen Adaptive Challenge ------------->|
   |                    |                     |<-- Evidence-Seeking Scenario -----------| (Save candidate_assessments)
   |                    |                     |                                         |
   |                    |                     |--- Tool: Lock Slot 101 ---------------->|
   |                    |                     |<-- ERROR: HTTP 409 Conflict ------------|
   |                    |                     |                                         |
   |                    |                     | [SELF-HEALING: RESOURCE CONFLICT]       |
   |                    |                     |--- Log Failure (tool_call_logs) ------->|
   |                    |                     |--- Strategy: Find Next Slot ------------>|
   |                    |                     |<-- Slot 102 Available ------------------|
   |                    |                     |--- Lock Slot 102 (Alternative) -------->|
   |                    |                     |<-- SUCCESS: Slot 102 Locked ------------|
   |                    |                     |--- Log Recovered (tool_call_logs) ----->|
   |                    |                     |                                         |
   |                    |<-- SSE Event Stream-|                                         |
   |                    |    (Execution Trace)|                                         |
   |                    |                     |                                         |
   |<- Display Evidence-|                     |                                         |
   |   & Review Card    |                     |                                         |
   |                    |                     |                                         |
   |--- Approve Action->|                     |                                         |
   |                    |--- POST /approve -->|                                         |
   |                    |                     |--- Dispatch Notification Webhook ------>|
   |                    |                     |--- Commit Final Slot Booking ---------->| (Save human_reviews)
   |                    |<-- OK 200 Finished -|                                         |
   |<- UI Updated ------|                     |                                         |
```

---

## 6. PARALLEL POLICY SIMULATOR (WHAT-IF ENGINE)

Layanan independen yang berjalan di luar antrean kandidat individual:

```
[HR Recruiter in Policy Tab]
            |
            | Pilih parameter simulasi:
            | [x] Toggle: Hapus syarat gelar sarjana (S1)
            | [x] Slider: Minimum pengalaman kerja (5 tahun -> 3 tahun)
            v
[FastAPI: Policy Simulator Endpoint]
            |
            | 1. Query cached pool kandidat job_id dari MariaDB
            | 2. Re-compute kualifikasi berbasis rule mutasi di memori
            | 3. Kalkulasi metrik komparasi (Baseline vs Simulated):
            v
+-----------------------------------------------------------------------------------------+
| SIMULATION RESULT                                                                       |
| - Eligible Candidates Pool: 124 -> 163 (+31.45%)                                        |
| - HR Review Workload Delta: +18.20% jam kerja tambahan                                  |
| - Average Technical Competency Score: 87.2 -> 86.4 (-0.92%)                             |
| - Trade-off Verdict: "Kandidat bertambah signifikan dengan penurunan kualitas < 1%.     |
|   Rekomendasi: Terapkan kebijakan jika target ekspansi mendesak."                       |
+-----------------------------------------------------------------------------------------+
            |
            v
[MariaDB: simulation_logs tersimpan untuk audit governance]
```

---

## 7. RUNDOWN VIDEO DEMO 3–5 MENIT (PITCHING & WALKTHROUGH)

* **00:00 - 00:20 | Hook Realistis:** 500 CV masuk, 66+ jam review manual, risiko kelelahan HR pada klaim ambigu.
* **00:20 - 00:40 | Job Analyzer & PII Masking:** Upload JD, muncul peringatan kriteria ambigu non-job-related, PII tersanitasi.
* **00:40 - 01:25 | Investigasi Kandidat A (Budi):** Ekstraksi bukti, skor 4D, penelusuran Candidate Evidence Graph transparan.
* **01:25 - 01:50 | Investigasi Kandidat B (Reza):** Deteksi overlap 17 bulan kerja, status `REVIEW_REQUIRED`, pembuatan Adaptive Challenge L2.
* **01:50 - 02:40 | Klimaks Self-Healing:**
  * Agen booking slot 101 -> Error HTTP 409 Conflict.
  * *"The agent failed. But the workflow didn't."*
  * Error classifier aktif -> Ambil slot 102 -> Status: `RECOVERED`.
* **02:40 - 03:20 | Human-in-the-Loop Approval & Audit:** HR klik Approve -> Email dispatched, DB ter-update.
* **03:20 - 04:00 | What-If Hiring Simulator:** Toggle syarat S1 -> Grafik trade-off terhitung seketika.
* **04:00 - 04:30 | Penutup & ROI:** Hemat 97.5% waktu, arsitektur deterministik teruji.
