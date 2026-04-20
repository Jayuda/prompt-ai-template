# Tools Documentation Template

<!--
  File ini mendokumentasikan semua tool (fungsi/API) yang bisa dipanggil oleh AI.
  Sesuaikan setiap bagian dengan tool yang kamu miliki.
-->

## Konfigurasi Endpoint

URL dan token untuk setiap tool dikonfigurasi di file `endpoints.json`.
Format:

```json
{
  "nama_tool": {
    "url": "https://your-api.com/endpoint",
    "token": "Bearer token-kamu-disini",
    "method": "GET atau POST"
  }
}
```

Untuk menambah tool baru dengan endpoint berbeda, cukup tambahkan entri baru di `endpoints.json` — tidak perlu ubah kode.

---

## Tools yang Tersedia

### function_one

<!--
  Ganti deskripsi ini sesuai fungsi tool kamu.
  Contoh: "Mencari data order pelanggan dari sistem e-commerce."
-->

[DESKRIPSI SINGKAT FUNGSI TOOL INI]

Gunakan tool ini **setiap kali** ada pertanyaan terkait [JENIS DATA — contoh: data pelanggan, status order, informasi produk].

**Parameter:**

<!--
  Tentukan parameter apa yang diterima tool ini.
  Tandai mana yang wajib (required) dan mana yang opsional.
-->

| Parameter | Tipe | Wajib | Keterangan |
|---|---|---|---|
| `param1` | string | Ya / Salah satu | [Deskripsi — contoh: ID pelanggan] |
| `param2` | string | Ya / Salah satu | [Deskripsi — contoh: email pelanggan] |

**Contoh response yang dikembalikan:**

```json
{
  "data": [
    {
      "id": "abc123",
      "field_satu": "nilai",
      "field_dua": 42,
      "field_tiga": true,
      "created_at": "2026-01-01 08:00:00"
    }
  ],
  "success": true,
  "message": "Record found"
}
```

<!--
  Ganti field di atas dengan field yang benar-benar dikembalikan oleh API kamu.
-->

**Keterangan field penting:**

| Field | Keterangan |
|---|---|
| `id` | Identifikasi unik data |
| `field_satu` | [Jelaskan artinya] |
| `field_dua` | [Jelaskan artinya] |
| `field_tiga` | [Jelaskan artinya, contoh: true = aktif, false = nonaktif] |
| `created_at` | Waktu data dibuat |

---

## Format Plan

AI harus mengembalikan objek JSON dengan array `plan`. Setiap langkah harus memiliki `step`, `action`, dan `parameters`.

```json
{
  "plan": [
    {
      "step": 1,
      "action": "nama_tool",
      "parameters": { "param1": "nilai" }
    }
  ]
}
```

---

### function_two

<!--
  Ini adalah tool kedua, biasanya untuk aksi write/destructive (mengubah data, mengirim pesan, dll).
  Hapus bagian ini jika hanya ada satu tool.
-->

[DESKRIPSI SINGKAT FUNGSI TOOL INI — contoh: Mengirim notifikasi ke pengguna, mengupdate status order, dll.]

Gunakan tool ini **setelah** mendapatkan [DATA YANG DIBUTUHKAN] ketika pengguna ingin [DESKRIPSI AKSI].

**Parameter:**

| Parameter | Tipe | Wajib | Keterangan |
|---|---|---|---|
| `id` | string | Ya | ID target yang dituju |
| `note` | string | Ya | Catatan atau payload tambahan |

**⚠️ Aturan khusus untuk aksi [NAMA_AKSI_BERBAHAYA — contoh: DELETE, RESET, CANCEL]:**
Sebelum menjalankan aksi ini, AI **wajib menanyakan konfirmasi** kepada pengguna:
> "Apakah Anda yakin ingin melakukan [NAMA_AKSI] untuk [IDENTITAS TARGET]?"
Baru jalankan tool ini jika pengguna menjawab **ya / iya / setuju**.

**Contoh plan — aksi read (aman, tanpa konfirmasi):**
```json
{
  "plan": [
    {
      "step": 1,
      "action": "function_two",
      "parameters": { "id": "abc123", "note": "cek status" }
    }
  ]
}
```

**Contoh plan — aksi destructive (sudah dikonfirmasi user):**
```json
{
  "plan": [
    {
      "step": 1,
      "action": "function_two",
      "parameters": { "id": "abc123", "note": "[NAMA_AKSI]" }
    }
  ]
}
```

---

## Contoh Plan Multi-Step

**Contoh — pengguna menyebut param1 (misal: ID):**
```json
{
  "plan": [
    {
      "step": 1,
      "action": "function_one",
      "parameters": { "param1": "nilai-dari-pengguna" }
    }
  ]
}
```

**Contoh — pengguna menyebut param2 (misal: email):**
```json
{
  "plan": [
    {
      "step": 1,
      "action": "function_one",
      "parameters": { "param2": "user@example.com" }
    }
  ]
}
```

**Contoh — multi-step: ambil data dulu, lalu lakukan aksi:**
```json
{
  "plan": [
    {
      "step": 1,
      "action": "function_one",
      "parameters": { "param1": "nilai-dari-pengguna" }
    },
    {
      "step": 2,
      "action": "function_two",
      "parameters": { "id": "{{step1.data.id}}", "note": "[AKSI]" }
    }
  ]
}
```

<!--
  Tambahkan tool baru dengan menduplikasi blok "### function_one" di atas
  dan mendaftarkan endpoint-nya di endpoints.json.
-->
