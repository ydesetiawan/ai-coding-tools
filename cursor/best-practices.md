# Cursor — Best Practices

Panduan ini untuk workflow **coding harian**, integrasi dengan **RubyMine**, **pintasan**, **hemat token** saat kerja berat, dan **pemilihan model** menurut kompleksitas tugas.

---

## Cursor + RubyMine (dua IDE, satu repo)

Cursor berbasis VS Code; RubyMine unggul di navigasi Ruby/Rails, debugger, database, test runner, dan refactor khusus bahasa.

| Situasi | Saran |
|--------|--------|
| Debug breakpoint, run spec, DB console | RubyMine |
| Refactor besar lintas file dengan AI | Cursor (Composer) |
| Edit cepat satu fungsi dengan instruksi natural | Cursor (Cmd+K) |
| Review diff & commit | Keduanya bisa; pilih yang nyaman |

**Praktis sehari-hari:** buka folder project yang sama di dua app (satu window RubyMine, satu Cursor). Hindari edit file yang sama bersamaan di dua editor tanpa save—refresh atau sync dulu. Untuk path Ruby/Rails, biarkan RubyMine mengelola SDK/gem; Cursor cukup baca file seperti editor biasa.

**Indexing:** `.cursorignore` tetap penting agar `vendor/bundle`, `tmp/`, `log/`, dan artefak Rails tidak membanjiri context AI.

Contoh tambahan untuk proyek Ruby/Rails:

```gitignore
vendor/bundle/
tmp/
log/
storage/
coverage/
.env*
```

---

## Kapan Pakai Cursor

✅ Edit presisi di file yang sudah ada (Cmd+K)  
✅ Refactor modul ukuran sedang (beberapa file) via Composer  
✅ Bekerja di codebase yang sudah ada dan sudah di-index  
✅ Developer yang nyaman dengan workflow VS Code  
❌ Autocomplete sederhana berulang — di RubyMine bisa cukup completion bawaan  
❌ Task reasoning sangat dalam tanpa batas waktu — pertimbangkan alat khusus atau model kuat dengan anggaran jelas  

---

## Pintasan penting (macOS)

Nilai default bisa diubah lewat **Cursor → Settings → Keyboard Shortcuts**. Cari nama fitur jika beda versi.

| Aksi | Pintasan umum |
|------|----------------|
| Inline edit (AI di editor) | **Cmd+K** |
| Chat panel | **Cmd+L** (atau sesuai binding di mesinmu) |
| Composer (multi-file) | **Cmd+I** atau **Cmd+Shift+I** |
| Command Palette | **Cmd+Shift+P** |
| Quick Open file | **Cmd+P** |
| Toggle sidebar | **Cmd+B** |
| Mention file/folder di chat | Ketik **@** lalu pilih file, folder, atau codebase |

**Tips:** satu kali map semua shortcut AI (Chat vs Composer vs Inline) supaya tidak salah buka panel yang lebih boros context.

---

## Workflow coding harian (ringkas)

1. **Mulai task:** tulis atau tempel requirement singkat di chat dengan **@file** / **@folder** yang relevan saja—bukan seluruh repo.
2. **Rencana vs langsung kode:** task besar → pakai **Plan Mode** (jika tersedia di versimu) atau minta AI buat checklist dulu, baru eksekusi.
3. **Implementasi:** perubahan kecil → **Cmd+K**; banyak file → **Composer** dengan scope jelas.
4. **Verifikasi:** jalankan test/linter di **RubyMine** atau terminal; perbaiki error dengan context error yang **dirangkum**, bukan log panjang utuh.
5. **Commit:** review diff di Git; kalau AI mengubah banyak, baca per file penting.

---

## Hemat token tapi tetap “heavy coding”

Tujuan: context kecil tapi bermakna, request sedikit tapi tajam.

1. **`.cursorignore`** — jangan index folder gem besar, build, log.
2. **Scope mention:** pakai **@file** / **@folder**; **@codebase** hanya saat benar-benar butuh pencarian luas.
3. **Rules project** (`.cursor/rules` atau rules di settings) — konvensi, stack, gaya commit: mengurangi pengulangan instruksi di setiap chat.
4. **Jangan tempel:** log 500 baris, seluruh response API, atau file binari. Rangkum: error utama, stack trace puncak, expectation vs actual.
5. **Satu prompt terstruktur** sering lebih irit daripada banyak chat bolak-balik tanpa konteks. Gabungkan: latar belakang + constraint + definisi selesai.
6. **Template error:** “File X, baris ~Y, error Z, sudah dicoba A/B, yang diharapkan …”
7. **Reuse context:** jika platform mendukung “pin” atau referensi ke pesan sebelumnya, gunakan daripada mengulang penjelasan panjang.
8. **Batch refactor:** satu Composer session dengan daftar file/area lebih hemat daripada sepuluh sesi kecil untuk hal yang sama.

---

## Pilih model menurut kompleksitas

Anggap ada tier **cepat/ekonomis**, **seimbang**, dan **maksimal**. Nama pastinya mengikuti daftar model di Cursor kamu (berubah seiring rilis).

| Tingkat | Contoh tugas | Strategi |
|--------|----------------|----------|
| **Rendah** | Rename, format, komentar kecil, satu fungsi jelas | Model ringan / cepat; **Cmd+K**; context minimal |
| **Sedang** | Refactor satu modul, tambah endpoint, update test terkait | Model seimbang; **Composer** dengan **@folder** terbatas |
| **Tinggi** | Arsitektur baru, bug halus lintas banyak layer, migrasi perilaku | Model paling mampu reasoning; rencana tertulis dulu; pecah jadi fase jika perlu |

**Aturan praktis:**

- Jika jawaban salah akan **membuang waktu review** — naikkan tier model atau persempit dulu context dengan **@file**.
- Jika tugas repetitif dan aturan sudah jelas di **rules** — tier bawah sering cukup.
- **Jangan** pakai model terberat untuk “tolong jelaskan satu baris” kecuali memang butuh nuansa hukum/keamanan.

---

## Best Practices (inti)

### 1. Cmd+K vs Composer

```
Cmd+K      → edit 1 fungsi / 1 blok kode
Composer   → refactor yang melibatkan beberapa file
```

Jangan buka Composer untuk hal yang bisa Cmd+K — biasanya lebih hemat.

### 2. Setup `.cursorignore`

Lihat bagian Ruby/Rails di atas; tambahkan pola yang sesuai project (misalnya `node_modules/` untuk front-end di monorepo).

### 3. Cursor Rules untuk konvensi project

Taruh aturan tetap di rules (bukan diulang tiap chat): gaya kode, testing, error handling, bahasa komentar.

### 4. Plan sebelum eksekusi (task besar)

Mintalah rencana atau gunakan Plan Mode supaya arah kerja bisa dikoreksi sebelum banyak file berubah.

---

## Tips tambahan

- **Import dari VS Code:** extension dan tema bisa diimpor untuk mengurangi gesekan saat pindah dari stack VS Code.
- **Composer + @:** `@file`, `@folder`, `@codebase` untuk context presisi.
- **Background Agents:** untuk tugas panjang yang bisa jalan sementara kamu fokus di RubyMine (sesuai paket/versi).

---

## Ringkasan satu baris

**RubyMine** untuk run/debug Ruby; **Cursor** untuk AI dengan **scope kecil**, **rules** jelas, **model naik turun** sesuai risiko task, dan **Cmd+K** dulu sebelum Composer.
