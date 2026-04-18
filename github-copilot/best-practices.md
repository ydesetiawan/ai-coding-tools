# GitHub Copilot — Best Practices

## Kapan Pakai GitHub Copilot

✅ Autocomplete harian saat coding  
✅ Generate boilerplate dan repetitive code  
✅ Code review dan PR di GitHub  
✅ Codebase yang butuh compliance ketat (enterprise-safe)  
✅ Developer yang tidak mau keluar dari VS Code / JetBrains  
❌ Tidak ideal untuk refactor arsitektur besar  
❌ Context window lebih kecil dibanding Claude Code / Cursor  

---

## Best Practices

### 1. Tulis komentar deskriptif sebagai "brief"
Copilot membaca komentar di atas fungsi sebagai konteks. Makin jelas komentar, makin relevan outputnya:

```typescript
// Fetches paginated list of orders for a given user
// Params: userId (string), page (number, 1-indexed), limit (default: 20)
// Returns: { orders: Order[], total: number, hasNextPage: boolean }
// Throws: NotFoundException if user not found
async function getUserOrders(userId: string, page: number, limit = 20) {
```

### 2. Nama yang semantik = autocomplete yang lebih baik
```typescript
// ❌ Nama tidak jelas — Copilot sulit prediksi
const d = getData();
function proc(x) {}

// ✅ Nama jelas — Copilot bisa prediksi dengan tepat
const userOrders = fetchUserOrders();
function validateEmailFormat(email: string) {}
```

### 3. Gunakan Copilot Chat untuk penjelasan kode
Copilot Chat (bukan hanya autocomplete) berguna untuk:
- Jelaskan kode yang tidak familiar
- Generate unit test dari fungsi yang sudah ada
- Translate kode dari satu bahasa ke bahasa lain

### 4. Review setiap suggestion sebelum Tab
Jangan `Tab` secara refleks. Baca suggestion, pastikan logikanya benar terutama untuk edge case.

### 5. Manfaatkan Copilot di GitHub PR
- **Copilot Code Review:** Minta review otomatis di PR sebelum reviewer manusia
- **Copilot Workspace:** Untuk planning perubahan dari GitHub Issues langsung

---

## Tips Spesifik

- **Shortcut penting:**
  - `Tab` → terima suggestion
  - `Esc` → tolak suggestion
  - `Alt + ]` / `Alt + [` → navigasi antar suggestion alternatif
  - `Ctrl + Enter` → buka panel semua suggestions

- **Untuk test generation:** Buka file test, tulis nama fungsi yang mau ditest sebagai komentar, biarkan Copilot generate test cases.

- **`.github/copilot-instructions.md`:** File ini bisa dipakai untuk memberikan context project ke Copilot secara permanen (nama project, stack, konvensi).
