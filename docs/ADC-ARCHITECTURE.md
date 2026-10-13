# ADC — Agency Digital Consultant
## Arsitektur AI Agent Full Stack v0.1 (Draft R&D)

**Tanggal:** 2026-10-12
**Pemilik produk / Governor:** Ridz
**Orkestrator R&D:** ChatGPT (lihat `ADC-CHATGPT-INSTRUCTIONS.md`)
**Level bukti saat ini:** E1 (desain). Belum ada klaim agentic/agnostic. Dokumen ini adalah rencana, bukan bukti.
**Rujukan Framework (dirujuk, TIDAK disalin):** `AAFA_BASELINE.md`, `AAFA-FSDW_v0_1_LOCKED.md`, `Universal_RnD_Starter_Pack_Plugin_Mapping_2026-10-12.md`, `USAGE-RULES.md`

> Framework dipakai sebagai **bahasa dan kompas**, bukan gerbang. Status "LOCKED" hanya berlaku di level Framework, bukan di proyek ADC.

---

## 1. Tujuan & Batas

**Tujuan.** ADC adalah AI Agent yang menjalankan peran **konsultan agensi digital**: menerima brief klien, mendiagnosis kondisi digital klien, menyusun strategi dan proposal, mengeksekusi pekerjaan digital yang disetujui, lalu melaporkan hasil, dengan jejak bukti di setiap langkah.

**Bukan tujuan (non-goals) v0.1**
- Bukan pengganti keputusan manusia untuk hal berkonsekuensi (kirim ke klien, belanja, publish, ubah sistem klien).
- Bukan platform multi-agent besar. Satu Agent Core, tiga modul domain.
- Bukan sistem yang wajib memakai semua plugin. Satu jalur emas dulu.
- Bukan produk berbayar di hari pertama. Free tier dulu sampai batasnya.

---

## 2. Arsitektur Berdasarkan Nama

Nama **Agency · Digital · Consultant** dipetakan menjadi tiga modul domain di dalam satu Agent Core.

| Kata | Pilar | Makna dalam sistem | Tanggung jawab | Output utama |
|---|---|---|---|---|
| **Consultant** (C) | Consulting Engine | Otak diagnosis dan rekomendasi | Intake brief, audit, analisis kompetitor, skoring peluang, roadmap, proposal, laporan | Findings, Recommendation, Roadmap, Proposal, Report |
| **Digital** (D) | Digital Capability Hub | Tangan eksekusi di kanal digital | Crawl/audit situs, riset kata kunci, konten, kalender sosial, analitik, prototipe landing page, email | Hasil audit teknis, draf konten, data analitik, prototipe |
| **Agency** (A) | Agency Operations | Mesin operasional agensi | Data klien, engagement, deliverable, approval, notifikasi, draf invoice, knowledge klien | Engagement record, Deliverable package, Approval log |

**Makna ganda "Agency".** Selain agensi sebagai bisnis, *agency* berarti **kewenangan yang didelegasikan**. Karena itu pilar A dan Control Plane (bagian 8) adalah satu kesatuan: ADC hanya bertindak dalam wewenang yang diberikan Ridz (rantai INTENT → AUTHORITY → PERMISSION → ACTION).

---

## 3. Prinsip Desain

1. **Loop nyata lebih penting daripada arsitektur megah.** Satu loop yang benar-benar jalan mengalahkan sepuluh layer di atas kertas.
2. **Capability dulu, vendor belakangan.** Core hanya meminta `cap.*`; adapter yang menerjemahkan ke provider.
3. **Satu jalur emas.** Satu provider per fungsi (satu deploy, satu workflow engine, satu analytics). Tambah provider hanya jika ada kebutuhan nyata.
4. **Free tier mentok.** Setiap tindakan yang memakai kuota dicatat di quota ledger; berhenti sebelum batas.
5. **Manusia memegang kewenangan.** Default L1 (eksekusi dengan persetujuan); read-only riset publik boleh L2.
6. **Trace sejak hari pertama.** Setiap run menulis jejak yang bisa direkonstruksi.
7. **Diminta ≠ dieksekusi ≠ berhasil.** Setiap aksi diverifikasi dengan read-back.
8. **Sesi ChatGPT ≠ runtime produksi.** ChatGPT membangun dan mengorkestrasi di fase awal; runtime ADC yang mandiri dibangun bertahap.
9. **Tanpa asumsi komputer lokal.** Semua langkah bisa dijalankan dari browser dan konektor (GitHub, Notion, deploy via cloud).

---

## 4. Arsitektur Logis

Delapan layer logis (bukan delapan service).

```text
 Ridz (Governor)
      │  goal / approval / keputusan
      ▼
┌──────────────────────────────────────────────────────────────┐
│ L1  INTERFACE                                                │
│     Fase awal: ChatGPT sebagai Operator Console              │
│     Fase lanjut: Operator Console + Client Portal (web)      │
└──────────────────────────────┬───────────────────────────────┘
                               ▼
┌──────────────────────────────────────────────────────────────┐
│ L2  ADC AGENT CORE  (agnostik provider)                      │
│     Goal · Context · State · Decision · Loop · Termination   │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐              │
│  │ C Consult  │  │ D Digital  │  │ A Agency   │  modul domain│
│  └────────────┘  └────────────┘  └────────────┘              │
└───────┬───────────────────────────────────────┬──────────────┘
        ▼                                       ▼
┌───────────────────────────────┐   ┌──────────────────────────┐
│ L3  CAPABILITY REGISTRY       │   │ L7  CONTROL PLANE        │
│     & RESOLVER                │◄──┤  autonomy · approval ·   │
│  cap.* → adapter yang cocok   │   │  budget/quota guard      │
└───────────────┬───────────────┘   └──────────────────────────┘
                ▼
┌──────────────────────────────────────────────────────────────┐
│ L4  ADAPTERS   (kontrak canonical → API provider)            │
└───────────────┬──────────────────────────────────────────────┘
                ▼
┌──────────────────────────────────────────────────────────────┐
│ L5  PROVIDERS / TOOLS   (GitHub, Firecrawl, Supabase, ...)   │
└───────────────┬──────────────────────────────────────────────┘
                ▼
        ENVIRONMENT: web publik, situs klien (dengan izin), file, API
                ▼
┌─────────────────────────────┐  ┌─────────────────────────────┐
│ L6  STATE & MEMORY          │  │ L8  TRACE & EVIDENCE        │
│  engagement, klien, run     │  │  jejak run, hasil read-back │
└─────────────────────────────┘  └─────────────────────────────┘
```

| Layer | Fungsi | Fase awal (gratis, tanpa DB) | Fase lanjut |
|---|---|---|---|
| L1 Interface | Titik kontak Ridz dan (nanti) klien | ChatGPT | Web console + portal |
| L2 Agent Core | Loop keputusan | Dijalankan ChatGPT berdasar spesifikasi | Kode runtime sendiri |
| L3 Registry | Katalog capability | File YAML di GitHub | Tabel + file |
| L4 Adapters | Terjemahan ke provider | Instruksi prosedural untuk ChatGPT | Modul kode |
| L5 Providers | Alat nyata | Konektor ChatGPT | Konektor + API |
| L6 State | Kondisi engagement | JSON/MD di GitHub | Supabase Postgres |
| L7 Control Plane | Wewenang dan kuota | `policies/*.yaml` + persetujuan Ridz di chat | Panel approval |
| L8 Trace | Rekonstruksi run | JSON di GitHub | Tabel trace + file |

---

## 5. Model Domain

### 5.1 Entitas inti

| Entitas | Isi singkat |
|---|---|
| Client | Identitas klien, industri, situs/kanal, batas izin akses |
| Engagement | Satu proyek konsultasi dengan satu klien, punya state dan goal |
| Brief / Goal | Tujuan terukur (contoh: "naikkan trafik organik 20% dalam 3 bulan") |
| Finding | Temuan audit: area, severity, bukti (URL/tangkapan), observasi |
| Recommendation | Tindakan yang disarankan, prioritas, estimasi dampak dan usaha |
| Roadmap | Urutan rekomendasi per periode |
| Proposal / SOW | Lingkup, deliverable, asumsi, harga (draf) |
| Deliverable | Hasil kerja yang diserahkan (laporan, konten, prototipe) |
| Approval | Permintaan dan keputusan manusia atas aksi berkonsekuensi |
| Run | Satu eksekusi loop agent untuk satu goal |
| TraceEvent | Satu langkah dalam Run |
| Capability / Provider / Policy | Katalog kemampuan, alat nyata, aturan wewenang |

### 5.2 Siklus hidup Engagement

```text
LEAD → DISCOVERY (C) → AUDIT (C+D) → STRATEGY (C) → PROPOSAL (C+A)
     → [APPROVAL: Ridz/klien] → EXECUTION (D) → REPORTING (C+A)
     → RETAINER | CLOSED (A)
```

Transisi mundur diperbolehkan (misalnya EXECUTION → STRATEGY) dan harus mencatat alasannya.

### 5.3 Siklus hidup Run

```text
PLANNED → RUNNING → AWAITING_APPROVAL → RUNNING → SUCCEEDED
                  ↘ FAILED (dengan rekomendasi pemulihan)
                  ↘ ESCALATED (butuh keputusan manusia)
                  ↘ ABORTED (dihentikan manusia atau guard kuota)
```

Setiap Run harus berakhir di salah satu status terminal dengan alasan tertulis.

---

## 6. Capability Catalog (Canonical, Bebas Vendor)

Konvensi ID: `cap.<domain>.<aksi>`. Kolom "Level" adalah default otonomi (L0 rekomendasi, L1 dengan approval, L2 dalam wewenang yang ditentukan). "Fase" menunjukkan kapan capability itu pertama dibangun.

### 6.1 Consultant (C)

| ID | Tujuan | Level | Fase |
|---|---|---|---|
| `cap.consult.intake_brief` | Mengubah brief mentah menjadi goal terukur dan daftar asumsi | L2 | P1 |
| `cap.consult.digital_audit` | Audit digital situs/kanal klien: SEO dasar, konten, performa, konversi | L2 (publik) / L1 (butuh akses) | P1 |
| `cap.consult.competitor_scan` | Pemetaan kompetitor dari sumber publik | L2 | P2 |
| `cap.consult.opportunity_scoring` | Skoring peluang berdasarkan dampak dan usaha | L2 | P2 |
| `cap.consult.strategy_roadmap` | Strategi dan roadmap berprioritas | L1 | P2 |
| `cap.consult.proposal_sow` | Draf proposal dan SOW | L1 | P2 |
| `cap.consult.report_generate` | Laporan hasil untuk klien | L1 | P1 |

### 6.2 Digital (D)

| ID | Tujuan | Level | Fase |
|---|---|---|---|
| `cap.digital.site_crawl` | Mengambil dan memetakan halaman situs | L2 (publik) | P1 |
| `cap.digital.seo_keyword_research` | Riset kata kunci dari sumber yang tersedia | L2 | P2 |
| `cap.digital.content_plan` / `content_draft` | Rencana dan draf konten | L1 | P2 |
| `cap.digital.social_calendar` | Kalender konten sosial (draf) | L1 | P3 |
| `cap.digital.analytics_read` | Membaca data analitik klien (read-only) | L1 | P3 |
| `cap.digital.ads_analysis` | Analisis performa iklan (read-only dulu) | L1 | P4 |
| `cap.digital.landing_prototype` | Prototipe landing page | L1 | P3 |
| `cap.digital.email_send` | Kirim email transaksional/outreach | L1 selalu | P4 |

### 6.3 Agency (A)

| ID | Tujuan | Level | Fase |
|---|---|---|---|
| `cap.agency.client_record` | Simpan dan baca data klien | L2 | P1 |
| `cap.agency.engagement_tracker` | Lacak state engagement | L2 | P1 |
| `cap.agency.deliverable_package` | Susun paket deliverable | L2 | P2 |
| `cap.agency.approval_request` | Ajukan dan catat keputusan approval | L2 | P1 |
| `cap.agency.invoice_draft` | Draf invoice (tidak pernah kirim otomatis) | L1 | P4 |
| `cap.agency.notify` | Notifikasi ke Ridz/klien | L1 | P3 |

### 6.4 Lintas-domain (X)

| ID | Tujuan | Level | Fase |
|---|---|---|---|
| `cap.core.web_research` | Riset web berkutip | L2 | P1 |
| `cap.core.doc_lookup` | Verifikasi dokumentasi teknis versi terkini | L2 | P1 |
| `cap.core.state_read` / `state_write` | Baca/tulis state | L2 | P1 |
| `cap.core.trace_write` | Tulis TraceEvent | L2 (wajib) | P1 |
| `cap.core.verify_readback` | Baca ulang efek aksi untuk verifikasi | L2 (wajib) | P1 |
| `cap.core.quota_check` | Cek dan catat pemakaian kuota provider | L2 (wajib) | P1 |

### 6.5 Template kontrak capability

```yaml
capability:
  id:
  purpose:
  input_schema:
  preconditions:
  execution_contract:
  output_schema:
  failure_modes:
  authority_required:
```

### 6.6 Contoh kontrak lengkap

```yaml
capability:
  id: cap.consult.digital_audit
  purpose: Menghasilkan temuan audit digital yang berbukti untuk satu situs klien.
  input_schema:
    target_url: string (wajib, milik klien atau publik)
    scope: [seo, content, performance, conversion]
    max_pages: integer (default 25)
    language: string (default "id")
  preconditions:
    - engagement.state in [DISCOVERY, AUDIT]
    - target diizinkan (publik, atau izin klien tercatat)
    - quota_check lulus untuk provider crawl
  execution_contract:
    - resolve capability cap.digital.site_crawl lewat adapter yang aktif
    - evaluasi cakupan (halaman terambil vs diminta)
    - jika cakupan kurang dari ambang, re-decide (kurangi scope, ganti provider, atau eskalasi)
    - analisis per area, tulis Finding dengan bukti
  output_schema:
    findings: [{id, area, severity, evidence_url, observation, recommendation}]
    coverage: {pages_requested, pages_fetched, notes}
    confidence: low | medium | high
  failure_modes:
    - site_unreachable
    - blocked_by_robots_or_terms
    - quota_exhausted
    - partial_coverage
    - provider_error
  authority_required: L2 untuk halaman publik; L1 jika butuh login atau akses sistem klien
```

---

## 7. Capability → Provider (Free-Tier-First)

Angka kuota diambil dari snapshot Starter Pack bagian 5 (2026-10-12). **Verifikasi ulang di dashboard resmi sebelum memakai.**

| Kebutuhan | Provider utama | Cadangan | Catatan kuota/risiko |
|---|---|---|---|
| Orkestrasi dan R&D | ChatGPT (paket yang dimiliki) | tidak ada | Sesi bukan runtime produksi; API OpenAI dihitung terpisah dan bukan bagian dari paket ChatGPT |
| Source of truth kode, state awal, trace | GitHub | tidak ada | Actions Free 2.000 menit/bulan |
| Riset web | Web search native ChatGPT | Firecrawl | Pakai web search dulu |
| Crawl dan scrape terstruktur | Firecrawl | web fetch ChatGPT | Free 1.000 credit/bulan; 1 credit per halaman; hormati robots/Terms |
| Dokumentasi teknis | Context7 | web search resmi | Verifikasi versi sebelum menulis kode |
| Database dan auth (P3+) | Supabase | file di GitHub | 500 MB DB, 1 GB storage; project idle bisa pause; wajib RLS |
| Deploy (P3+) | Satu dari: Vercel atau AppDeploy | alternatif lain yang dievaluasi | **Vercel Hobby hanya non-komersial.** Jika ADC melayani klien berbayar, evaluasi plan/alternatif sebelum publish |
| Workflow lintas-aplikasi (P4) | n8n **atau** Inngest (pilih satu) | tidak ada | n8n self-host butuh hosting dan cek lisensi; Inngest cocok untuk job di dalam aplikasi |
| Email transaksional (P4) | Resend | draf manual | 100 email/hari, 3.000/bulan |
| Analitik produk (nanti) | PostHog **atau** Amplitude | tidak ada | Pilih satu; redaksi PII |
| Data marketing klien (P3+) | Windsor.ai | ekspor manual dari klien | Free tier belum terverifikasi; cek akun; izin per sumber data |
| UI/prototipe | Figma | draf HTML di repo | Hanya jika UI material |
| Keamanan | Strix | tinjauan manual | Hanya pada target yang dimiliki/diotorisasi |
| Model runtime mandiri (P3+) | satu default + satu cadangan yang diuji | Hugging Face untuk eksperimen | Kuota HF sangat kecil; bukan untuk produksi |
| Catatan keputusan | Notion (opsional) | Markdown di GitHub | Jangan menduplikasi artefak yang sudah canonical di GitHub |

**Ditunda dari inti:** Replit, Lovable, Linear, HeyGen, Higgsfield, invideo, Plugin Creator. Alasan ada di Starter Pack bagian 8.

---

## 8. Control Plane: Wewenang, Approval, Kuota

### 8.1 Klasifikasi wewenang

| Kelas aksi | Contoh | Default | Aturan |
|---|---|---|---|
| Baca publik | web search, crawl halaman publik, baca dokumentasi | L2 | Otomatis, tetap di-trace dan dihitung kuotanya |
| Tulis internal ADC | tulis state/trace/draf di repo sandbox | L2 | Otomatis, wajib read-back |
| Konsekuensi rendah | buat branch/PR, draf laporan | L1 | Tampilkan rencana, tunggu "ya" dari Ridz |
| Konsekuensi tinggi | kirim apa pun ke klien, publish, deploy produksi, email, ubah sistem klien, keluarkan uang | L1 wajib | Tidak pernah otomatis |
| Dicadangkan untuk Ridz | menaikkan level otonomi, mengubah aturan wewenang, menambah provider berbayar, memberi/mencabut akses | Keputusan langsung | Tidak boleh didelegasikan |
| Terlarang | scan aktif ke target tanpa izin, menyimpan secret di chat/repo, melewati approval | Diblokir | Tidak ada pengecualian |

Level otonomi dapat naik per kelas aksi seiring bukti jalan (E3/E4), atas keputusan Ridz, bukan otomatis.

### 8.2 Quota guard

- Quota ledger berisi: provider, kuota resmi, tanggal verifikasi, pemakaian, ambang.
- Ambang: **peringatan 70%**, **berhenti 90%** (kecuali Ridz memutuskan lain).
- Sebelum aksi berkuota: `cap.core.quota_check`. Jika tidak lulus, pilih cadangan gratis atau eskalasi. Tidak pernah otomatis naik ke plan berbayar.

---

## 9. Loop Agentik ADC

```text
GOAL → CONTEXT/STATE → DECISION → CAPABILITY SELECTION → ACTION
     → OBSERVATION → EVALUATION → STATE UPDATE → NEXT DECISION
     → SELESAI | PULIH | ESKALASI
```

### 9.1 Contoh run emas: Audit Digital

| Langkah | Isi |
|---|---|
| Goal | "Audit situs X untuk klien Y; hasilkan laporan temuan prioritas" |
| Decision | Pilih `cap.digital.site_crawl` lalu `cap.consult.digital_audit` |
| Action | Crawl hingga 25 halaman publik |
| Observation | 18 dari 25 halaman terambil, 7 diblokir |
| Evaluation | Cakupan 72% di bawah ambang 80% |
| Re-decision | Coba cadangan (web fetch untuk halaman kunci) atau turunkan scope dan catat batasan |
| Action | Analisis dan tulis Finding berbukti |
| Verification | Baca ulang file laporan, cek setiap Finding punya bukti |
| Approval | Minta persetujuan Ridz sebelum laporan dianggap final |
| Termination | SUCCEEDED dengan catatan cakupan, atau ESCALATED |

### 9.2 Skema trace minimum

```json
{
  "run_id": "run-2026-10-12-001",
  "engagement_id": "eng-demo-001",
  "goal": "Audit situs X",
  "events": [
    {
      "seq": 1,
      "ts": "2026-10-12T10:00:00Z",
      "decision": "crawl 25 halaman publik",
      "capability": "cap.digital.site_crawl",
      "provider": "firecrawl",
      "authority": {"level": "L2", "approval": "not_required"},
      "action": "crawl(max_pages=25)",
      "observation": {"pages_fetched": 18, "errors": 7},
      "evaluation": "coverage 72% < 80%",
      "state_transition": "AUDIT: crawling -> insufficient_coverage",
      "next_decision": "fallback ke web fetch untuk halaman kunci",
      "quota": {"provider": "firecrawl", "used": 18}
    }
  ],
  "termination": {"status": "SUCCEEDED", "reason": "laporan terverifikasi, cakupan 88%"}
}
```

---

## 10. State & Data

### 10.1 Strategi bertahap (hemat dan gratis)

| Fase | Penyimpanan | Alasan |
|---|---|---|
| P0 sampai P2 | File JSON/MD di repo GitHub | Nol biaya, versi dan riwayat otomatis, cukup untuk membuktikan loop |
| P3 dan seterusnya | Supabase Postgres | Saat butuh multi-akses, portal, dan query |

### 10.2 Aturan data (berlaku sejak awal)

- Satu klien = satu batas data (`client_id`). Saat pindah ke Supabase, terapkan RLS per `client_id`.
- Minimalkan PII klien. Simpan yang perlu saja.
- Kunci privileged (service-role) hanya server-side; tidak pernah di browser, repo, chat, atau log.
- Data klien tidak dikirim ke provider yang belum ditetapkan di Provider Registry.

---

## 11. Struktur Repositori (Satu Repo)

```text
adc/
├── README.md                    # ringkasan + cara menjalankan run pertama
├── docs/
│   ├── ADC-ARCHITECTURE.md      # dokumen ini (canonical)
│   ├── DECISIONS.md             # log keputusan Ridz + alasan
│   └── PROVIDERS.md             # registri provider (status, kuota, lock-in)
├── capabilities/                # satu YAML per capability (kontrak canonical)
├── adapters/                    # catatan/kode terjemahan capability → provider
├── policies/
│   ├── authority.yaml           # level otonomi per kelas aksi
│   └── quota-ledger.yaml        # kuota dan pemakaian per provider
├── state/                       # engagement, client, run (fase awal)
├── traces/                      # satu file JSON per run
├── deliverables/                # laporan dan hasil kerja
├── tests/                       # skenario uji (sukses, gagal paksa, approval)
└── ops/
    └── SESSION-LOG.md           # ringkasan tiap sesi ChatGPT
```

Rujukan Framework tidak disalin ke repo; cukup tautan/nama file di `README.md`.

---

## 12. Roadmap Bertahap

Setiap fase punya **Definition of Done (DoD)** yang ringan. Ini bukan gerbang Framework.

| Fase | Fokus | DoD |
|---|---|---|
| **P0 Fondasi** | Repo, struktur, registry capability, skema trace, authority dan quota ledger awal | Repo ada, kontrak capability P1 tertulis, skema trace tervalidasi |
| **P1 Irisan Vertikal** | Satu run penuh: `intake_brief` → `site_crawl` → `digital_audit` → `report_generate` → approval | Satu run nyata pada situs sandbox/publik menghasilkan laporan dan trace; satu uji gagal paksa menunjukkan re-decision tanpa mengarang sukses |
| **P2 Pipeline Konsultasi** | Kompetitor, skoring, strategi, proposal, deliverable package | Satu engagement bergerak DISCOVERY sampai PROPOSAL dengan approval tercatat |
| **P3 Persistensi dan Portal** | Supabase (RLS), portal klien sederhana, satu jalur deploy | Data tersimpan dan terbaca ulang; akses antar-klien terisolasi; deploy terverifikasi (status terminal dan URL dibuka) |
| **P4 Otomasi** | Satu workflow engine, email, analitik dasar | Satu alur terjadwal berjalan idempoten dengan log dan read-back |
| **P5 Pengerasan Agnostik** | Uji tukar model/provider/adapter/storage | Hanya jika ada klaim publik "agnostic/agentic"; saat itu bukti E4/E5 disiapkan |

### Rencana hari pertama (P0 + awal P1)

1. Buat repo sandbox dan struktur folder bagian 11.
2. Tulis kontrak capability P1 (6 kontrak: intake_brief, site_crawl, digital_audit, report_generate, approval_request, verify_readback).
3. Tulis `authority.yaml`, `quota-ledger.yaml` (kuota diverifikasi dari sumber resmi), dan skema trace.
4. Jalankan run pertama manual-terbimbing (mode M0, lihat bagian 13) pada situs publik atau situs milik sendiri.
5. Simpan trace dan laporan, catat sisa kuota, tulis ringkasan sesi.

---

## 13. Mode Orkestrasi ChatGPT

Dua peran yang tidak boleh tertukar:

| Peran | Siapa | Tugas |
|---|---|---|
| **Builder-Orchestrator** | ChatGPT | Membangun ADC dan mengorkestrasi konektor selama R&D |
| **ADC Runtime Agent** | ADC itu sendiri | Menjalankan loop untuk klien secara mandiri (fase lanjut) |

| Mode | Deskripsi | Otonomi | Biaya |
|---|---|---|---|
| **M0 Manual-terbimbing** | ChatGPT menjalankan loop langkah demi langkah di sesi; Ridz menyetujui; trace ditulis ke repo | L0 sampai L1 | Gratis |
| **M1 Terbantu konektor** | ChatGPT memanggil konektor (GitHub, Firecrawl, Supabase, dst) dengan approval per aksi berkonsekuensi | L1 sampai L2 | Dalam kuota gratis |
| **M2 Otomatis** | Runtime ADC memakai adapter model dan satu workflow engine; Ridz memantau lewat panel | L2 ke atas, naik per bukti | Perlu keputusan biaya terpisah (API model) |

Awali di M0. Naik mode hanya setelah mode sebelumnya menghasilkan run nyata dengan trace.

---

## 14. Keputusan yang Dipegang Ridz

Ridz menetapkan kontrak produk; ChatGPT mengusulkan "bagaimana".

| # | Keputusan | Usulan default | Mempengaruhi |
|---|---|---|---|
| K1 | Niche/target klien awal | UMKM dengan situs/toko online | Skema audit, template laporan |
| K2 | ADC dipakai komersial atau hanya R&D | Mulai R&D | Pilihan deploy (Vercel Hobby non-komersial) |
| K3 | Bahasa laporan | Indonesia, Inggris opsional | Template |
| K4 | Situs uji pertama | Situs milik sendiri atau situs publik tanpa akses login | Run P1 |
| K5 | Layanan awal yang ditawarkan | Audit digital + roadmap | Capability P1 sampai P2 |
| K6 | Kapan naik dari M0 ke M1 | Setelah satu run M0 lengkap | Level otonomi |

---

## 15. Status Klaim

| ID | Klaim | Level bukti | Status |
|---|---|---|---|
| CL-01 | Arsitektur ADC terdefinisi | E1 | Ada (dokumen ini) |
| CL-02 | Kontrak capability P1 tertulis | E1 → E2 | Belum |
| CL-03 | Loop audit berjalan nyata | E3/E4 | Belum |
| CL-04 | ADC agentic / agnostic | E4/E5 | Tidak diklaim |

Tidak ada klaim yang dinaikkan tanpa bukti. Untuk penggunaan internal dan prototipe, tidak ada gerbang; gerbang bukti berlaku hanya jika ADC diklaim "agentic/agnostic/proven" ke publik.

---

## 16. Pemetaan ke Framework (Rujukan Saja)

| Elemen ADC | Rujukan |
|---|---|
| Loop agentik | AAFA-01, AAFA-02 |
| Capability, adapter, provider | AAFA-04, AAFA-05 |
| Wewenang dan level otonomi | AAFA-07 |
| Diminta ≠ dieksekusi ≠ berhasil, pemulihan, trace | AAFA-08, FSDW S09 |
| Blind spot yang dihindari (arsitektur sebelum bukti, tool ≠ agensi) | AAFA-12 |
| Katalog plugin, kuota, jalur emas | Starter Pack bagian 2, 4, 5, 8 |
| Aturan pemakaian Framework | USAGE-RULES |

---

**Versi:** 0.1 draft — 2026-10-12
**Revisi berikutnya:** setelah run P1 pertama, berdasarkan temuan nyata.
