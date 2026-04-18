# RubyMine + Cursor AI — Best Practices

Panduan praktis memakai **RubyMine** sebagai IDE utama untuk Ruby/Rails, dan **Cursor** (Pro $20) sebagai "AI coworker" untuk edit, refactor, dan eksplorasi kode — tanpa boros quota.

> Prinsip utama: **RubyMine yang menjalankan & mengerti Ruby, Cursor yang mengetik & merencanakan**.

---

## 1. Peran masing-masing tool

| Tugas | Pakai | Alasan |
|-------|-------|--------|
| Run & debug spec, breakpoint, step-into | **RubyMine** | Debugger Ruby paling matang |
| Rails console, DB tool, HTTP client | **RubyMine** | Built-in, terintegrasi SDK/gem |
| Navigasi simbol Ruby, "Find Usages", rename refactor safe | **RubyMine** | Memahami AST Ruby |
| Git diff, conflict resolve visual | **RubyMine** | Nyaman untuk review |
| Edit fungsi pakai instruksi natural | **Cursor (Cmd+K)** | Cepat & presisi |
| Refactor lintas file dengan plan | **Cursor (Composer / Agent)** | Konteks multi-file |
| Generate boilerplate, test skeleton | **Cursor** | Hemat waktu ketik |
| Eksplorasi codebase baru | **Cursor chat + @folder** | Tanya-jawab ringan |

**Aturan praktis:** buka project yang sama di dua IDE. Edit AI di Cursor, lalu pindah ke RubyMine untuk run/debug. Selalu **save di satu sisi, reload di sisi lain** sebelum mulai ngetik lagi — hindari konflik editor.

---

## 2. Setup awal (sekali kerja, manfaat lama)

### 2.1 `.cursorignore` untuk project Ruby/Rails

Agar indexing Cursor tidak membengkak dan jawaban AI lebih tajam:

```gitignore
vendor/bundle/
tmp/
log/
storage/
coverage/
public/assets/
public/packs/
node_modules/
.bundle/
.yardoc/
.env*
*.log
db/*.sqlite3
```

### 2.2 Cursor Rules untuk gaya Ruby/Rails

Taruh di `.cursor/rules/ruby.md` (atau `AGENTS.md` jika kamu pakai format itu). Contoh konten:

```md
- Bahasa: Ruby 3.x, Rails 7.x (sesuaikan).
- Style: ikuti RuboCop project; jangan ubah konfigurasi .rubocop.yml tanpa diminta.
- Test: RSpec. Gunakan `describe`/`context`/`it` deskriptif. Satu expectation per `it` bila memungkinkan.
- Service object: letakkan di app/services, nama akhiran `Service`, method publik `call`.
- Hindari callback ActiveRecord untuk logika non-persistence; pindahkan ke service.
- JANGAN buka koneksi N+1; pakai `includes`/`preload` bila iterasi relasi.
- Error: raise custom error di `app/errors`, bukan `StandardError` generik.
- Komentar & commit message: Bahasa Inggris singkat.
```

Keuntungan: aturan ini "selalu ikut" tanpa perlu diulang di tiap prompt → hemat token.

### 2.3 RubyMine: integrasi yang membantu Cursor

- Aktifkan **"Reload from disk"** otomatis (Settings → Appearance & Behavior → System Settings → Synchronize files) supaya perubahan dari Cursor langsung terbaca.
- **External tool** untuk buka file cepat di Cursor dari RubyMine (opsional): tambahkan tool yang menjalankan `cursor "$FilePath$"`.
- Pastikan **RuboCop / Standard** terdeteksi di RubyMine → feedback cepat setelah AI ngoprek.
- Jalankan spec via RubyMine test runner (gutter icon), biar hasilnya rapi dan debuggable.

---

## 3. Prompting untuk Agent Mode (daily coding)

Agent Mode / Composer bekerja paling baik kalau kamu kasih **tujuan + batasan + definisi selesai**. Hindari prompt "ajaib satu kalimat".

### 3.1 Struktur prompt minimal

```
Tujuan: <apa yang ingin dicapai, 1–2 kalimat>
Konteks: @file:..., @folder:...  (hanya yang relevan)
Batasan:
- Jangan ubah API publik
- Ikuti konvensi RSpec project
- Jangan tambah gem baru
Definisi selesai:
- Spec baru hijau
- Tidak ada rubocop offense baru
```

### 3.2 Contoh prompt yang baik (Rails)

**Bad:**
> "Tolong bikin fitur invoice."

**Good:**
> Tujuan: tambah endpoint `POST /api/v1/invoices` yang membuat invoice dari `order_id`.
> Konteks: @file:app/controllers/api/v1/orders_controller.rb, @folder:app/services/invoices
> Batasan: pakai service `Invoices::CreateFromOrder`; tulis request spec; jangan ubah routing lain.
> Definisi selesai: spec `spec/requests/api/v1/invoices_spec.rb` hijau; response JSON `{ id, number, total }`.

### 3.3 Pola prompt harian

- **Bugfix:** "Di @file ... baris ~N terjadi error X saat Y. Ekspektasi Z. Stack trace ringkas: ... Perbaiki tanpa mengubah perilaku publik. Tambahkan regression spec."
- **Refactor:** "Ekstrak logika di @file method `foo` menjadi service `Bar::Baz` di `app/services/bar`. Jangan ubah behavior. Pastikan spec lama tetap hijau."
- **Test:** "Tulis RSpec untuk @file kelas `X`. Cakup: happy path, edge case A, error case B. Pakai `let` & `subject`. Stub HTTP pakai WebMock."
- **Migration:** "Buat migration tambah kolom `status` (enum `draft/sent/paid`) di tabel `invoices`, beserta update model & spec. Jangan backfill dulu."

### 3.4 Aturan emas Agent Mode

1. **Tunjuk file dulu, jangan suruh cari sendiri.** `@file` > `@folder` > `@codebase`.
2. **Minta rencana dulu untuk task besar** ("Buat plan langkah-langkah, jangan modifikasi file dulu"). Setujui → baru eksekusi.
3. **Batasi scope perubahan.** Katakan "hanya sentuh file X, Y" jika perlu.
4. **Selalu minta test** bila menambah perilaku baru.
5. **Verifikasi di RubyMine** (run spec, jalankan app) — jangan percaya "done" dari AI tanpa bukti.

---

## 4. Hemat quota Cursor Pro ($20)

Cursor Pro punya batas request bulanan (jumlah persisnya berubah seiring paket). Prinsip: **kurangi request boros, maksimalkan context padat**.

### 4.1 Pilih panel yang tepat

| Kebutuhan | Panel | Biaya context |
|-----------|-------|----------------|
| Ubah 1 fungsi di file terbuka | **Cmd+K (inline)** | Paling kecil |
| Tanya/jelaskan kode | **Chat** | Kecil |
| Multi-file, butuh plan | **Composer / Agent** | Besar |

**Jangan** buka Agent untuk rename variabel — itu tugas Cmd+K atau refactor RubyMine (Shift+F6).

### 4.2 Taktik hemat token

1. **Scope context secara eksplisit.** Ketik `@file:path/ke/file.rb` bukan `@codebase`. `@codebase` mahal & sering blur.
2. **Buang output panjang.** Jangan tempel log 1000 baris. Rangkum: 5 baris error teratas + 5 baris sekitarnya.
3. **Satu prompt padat > 10 prompt kecil.** Setiap balasan baru = tagihan request. Gabungkan niatmu.
4. **Gunakan Rules, bukan pengulangan.** "Pakai RSpec, pakai service object di app/services" → taruh di rules, bukan di tiap prompt.
5. **Stop dialog kalau sudah jelas.** Kalau AI menawarkan "mau saya lanjut ke X, Y, Z?" dan kamu cuma butuh X — jawab pendek & spesifik.
6. **Hindari "regenerate" iseng.** Re-run sama dengan menambah request tanpa perubahan input.
7. **Tab completion** (autocomplete) biasanya tidak memakan quota premium sebesar chat/agent — manfaatkan untuk ketikan rutin.
8. **Matikan "auto-include open tabs"** kalau kamu suka buka banyak file — context membengkak diam-diam.
9. **Commit sering** → setelah satu task selesai, mulai chat baru yang pendek daripada melanjutkan thread panjang (thread panjang = context ikut dikirim berulang).
10. **Cache mentalmu:** simpan prompt template yang bagus di snippet/notes, reuse.

### 4.3 "Red flag" boros quota

- Thread chat > 30 pesan tentang task yang sama → mulai baru.
- Selalu pakai model paling mahal untuk hal sepele.
- Menempel seluruh file 2000 baris saat hanya butuh 1 method.
- Mengulang prompt karena instruksi kurang jelas — investasi 30 detik merapikan prompt = hemat 5 request.

---

## 5. Pilih model dengan bijak

Nama model berubah seiring rilis Cursor, jadi fokus ke **tier** — bukan nama.

| Tier | Cocok untuk | Contoh tindakan |
|------|-------------|------------------|
| **Cepat / ekonomis** | Edit kecil, rename, format, komentar, jawaban faktual singkat | Cmd+K di 1 fungsi, generate docstring, tulis 1 unit test sederhana |
| **Seimbang (default harian)** | Refactor 2–5 file, tambah endpoint standar, bugfix terisolasi, tulis spec | Composer untuk modul kecil, diagnosa error yang jelas stack-trace-nya |
| **Maksimal (reasoning)** | Arsitektur, bug halus lintas layer, migrasi perilaku, review desain | Plan dulu, eksekusi bertahap; jangan pakai untuk pekerjaan trivial |

### 5.1 Heuristik memilih

- **Apakah jawaban salah akan mahal** (bug produksi, data migration)? → naikkan tier.
- **Apakah aturan & pola sudah jelas** di rules? → tier ekonomis sering cukup.
- **Apakah kamu butuh eksplorasi / brainstorming**? → tier seimbang, bukan maksimal (lebih cepat iterasi).
- **Apakah kamu akan re-run beberapa kali**? → mulai dari tier ekonomis, naikkan hanya kalau kurang.

### 5.2 Pola hybrid yang hemat

1. **Plan dengan model kuat, eksekusi dengan model ekonomis.**
   - Prompt 1 (model kuat): "Buat rencana langkah-langkah refactor X, jangan modifikasi file."
   - Prompt 2+ (model ekonomis): eksekusi langkah 1, 2, 3 mengikuti rencana.
2. **Auto / Default** di Cursor biasanya cukup untuk 70% daily coding. Naikkan manual hanya saat perlu.
3. **Jangan tukar model di tengah thread** tanpa alasan — context dikirim ulang.

---

## 6. Workflow daily coding (contoh 1 hari)

1. **Pagi — stand-up task:** buka RubyMine, pull latest, buka Cursor di project sama.
2. **Pahami task:** di Cursor chat, "Jelaskan peran @folder:app/services/billing dalam alur invoice" (model seimbang, 1 prompt).
3. **Plan:** minta Agent bikin rencana perubahan — review, koreksi, setuju.
4. **Eksekusi bertahap:**
   - Perubahan kecil → Cmd+K langsung di editor.
   - Perubahan multi-file → Composer dengan `@file` eksplisit.
5. **Test di RubyMine:** run spec lewat gutter, debug kalau merah. Jangan minta AI menebak error kalau kamu bisa lihat stack trace sendiri.
6. **Perbaiki dengan prompt ringkas:** tempel **ringkasan** error, bukan seluruh log.
7. **RuboCop clean-up:** `bundle exec rubocop -a` di terminal RubyMine. Sisanya minta Cursor perbaiki offense yang tersisa.
8. **Review diff di RubyMine** (Git tool window) — baca file penting manual.
9. **Commit pesan** boleh dibantu Cursor (hemat: prompt pendek "Tulis commit message conventional untuk diff ini" + paste diff ringkas).
10. **Tutup thread** panjang → thread baru untuk task berikutnya.

---

## 7. Keamanan & kualitas

- **Jangan kirim rahasia** (API key, kredensial DB) ke AI. Masukkan `.env*` ke `.cursorignore`.
- **Audit perubahan migration & security-sensitive code** secara manual — AI bisa salah subtle.
- **Selalu jalankan test** sebelum commit; AI "yakin berhasil" ≠ benar.
- **Pakai feature branch**; rollback lebih mudah kalau eksperimen AI meleset.
- **Pair dengan `git diff`** — biasakan `git add -p` untuk approve hunk demi hunk hasil AI.

---

## 8. Checklist cepat (tempel di dinding)

- [ ] `.cursorignore` sudah berisi `vendor/bundle`, `tmp`, `log`, `.env*`.
- [ ] `.cursor/rules` memuat konvensi Ruby/Rails project.
- [ ] Task kecil → Cmd+K. Task besar → Agent dengan plan dulu.
- [ ] `@file` / `@folder` selalu eksplisit, hindari `@codebase`.
- [ ] Prompt: Tujuan + Konteks + Batasan + Definisi selesai.
- [ ] Model: default untuk harian, naikkan hanya saat perlu reasoning.
- [ ] Test dijalankan di RubyMine sebelum commit.
- [ ] Thread baru tiap task besar — jangan bikin thread raksasa.

---

## Ringkasan satu baris

**RubyMine** untuk menjalankan & mengerti Ruby. **Cursor** untuk AI dengan **scope presisi**, **rules tetap**, **prompt terstruktur**, dan **model seperlunya** — itulah cara menjaga $20 Pro tetap cukup sambil coding berat tiap hari.
