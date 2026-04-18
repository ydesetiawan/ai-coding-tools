# Google Antigravity — Best Practices

## Kapan Pakai Antigravity

✅ Prototyping UI / frontend baru dari scratch  
✅ Task yang butuh verifikasi visual di browser  
✅ Eksperimen fitur baru (gratis = bebas coba)  
✅ Task yang bisa diparalelkan antar sub-agent  
✅ Integrasi dengan Google Cloud / Firebase  
❌ Belum ideal untuk codebase enterprise yang butuh compliance ketat  
❌ Context window bisa ter-truncate untuk codebase yang sangat besar  

---

## Best Practices

### 1. Manfaatkan Browser Agent untuk UI
Daripada generate kode lalu test manual, minta Antigravity verifikasi langsung:
```
"Build a responsive product card component, then open the browser and verify it looks correct on mobile viewport (375px width)"
```

### 2. Gunakan Agent Manager untuk task paralel
Pisahkan task yang independent dan jalankan bersamaan:
- Agent 1: Buat komponen Header
- Agent 2: Buat komponen Footer  
- Agent 3: Setup routing

### 3. Tulis deskripsi task yang berorientasi outcome
Antigravity dirancang untuk beroperasi di level task, bukan instruksi per baris:
```
# ❌ Terlalu teknis
"Create a useState hook for the modal and add an onClick handler to the button"

# ✅ Berorientasi outcome
"Add a modal that opens when the user clicks 'View Details' on a product card. The modal should show product name, price, and description."
```

### 4. Gunakan `.agent/rules/` untuk konvensi permanen
Taruh aturan project (naming convention, library yang dipakai, hal yang dilarang) di `.agent/rules/` agar semua session konsisten.

---

## Tips Spesifik

- **Masih gratis (April 2026):** Maksimalkan penggunaan untuk eksperimen sebelum ada pricing.
- **AI Studio integration:** Bagus untuk rapid prototyping dan validasi ide sebelum commit ke implementasi penuh.
- **Untuk solo developer:** Saat ini lebih optimal untuk solo dev — fitur kolaborasi tim masih terbatas.
