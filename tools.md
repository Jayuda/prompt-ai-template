## Konfigurasi Endpoint

URL dan token untuk setiap tool dikonfigurasi di file `prompts/endpoints.json`.
Format:

```json
{
  "nama_tool": {
    "url": "https://...",
    "token": "Bearer token disini"
  }
}
```

Untuk menambah tool baru dengan endpoint berbeda, cukup tambahkan entri baru di `endpoints.json` — tidak perlu ubah kode.

---

## Tools yang Tersedia

### function_one
Mencari data perangkat  pelanggan dari sistem YourCompany.

Gunakan tool ini **setiap kali** ada pertanyaan terkait data perangkat, data, atau langganan pelanggan.

**Parameter (wajib isi salah satu):**
- `param1` — parameter ke-1 yang dilempar ke function_one
- `param2` — parameter ke-2 yang dilempar ke function_one

**Contoh response yang dikembalikan:**
```json
{
  "data": [
    {
      "id": "908asdui",
      "last_speed": 0,
      "last_engine": 0,
      "last_battery": 5,
      "last_time": "2026-04-14 12:40:14",
      "movement_status": "MISS",
      "mileage": 196.30,
      "enabled": true
    }
  ],
  "success": true,
  "message": "Record found"
}
```

**Keterangan field penting:**
| Field | Keterangan |
|---|---|
| `id` | id data |
| `last_speed` | Kecepatan terakhir (km/h) |
| `last_engine` | Status mesin (1 = on, 0 = off) |
| `last_battery` | Level baterai perangkat (%) |
| `last_time` | Waktu terakhir data diterima |
| `movement_status` | Status gerak: `MOVING` / `IDLE` / `STOP` / `MISS` |
| `mileage` | Total jarak tempuh (km) |
| `simcard_expired` | Tanggal kadaluarsa simcard / masa aktif layanan |
| `enabled` | Apakah perangkat aktif (true/false) |

---

## Format Plan

Kembalikan objek JSON dengan array `plan`. Setiap langkah harus memiliki `step`, `action`, dan `parameters`.

---

### function_two
Mengirim command ke perangkat  pelanggan untuk mengetahui kondisi perangkat secara real-time, atau melakukan restart.

Gunakan tool ini **setelah** mendapatkan ID perangkat (dari `function_one` atau dari pelanggan langsung) ketika pelanggan menanyakan status kondisi perangkat secara langsung, atau ingin me-restart perangkat.

**Parameter (keduanya wajib):**
- `id`    — ID perangkat yang dituju (contoh: `"98asdikuqhweuiq"`)
- `note` — catatan di param

**⚠️ Aturan khusus untuk `RESET#`:**
Sebelum menjalankan command `RESET#`, kamu **wajib menanyakan konfirmasi** kepada pelanggan:
> "Apakah Anda yakin ingin me-restart perangkat  dengan ID [id]?"
Baru jalankan tool ini jika pelanggan menjawab **ya / iya / setuju**.

**Contoh plan — cek status perangkat:**
```json
{
  "plan": [
    {
      "step": 1,
      "action": "function_two",
      "parameters": { "id": "98asdikuqhweuiq", "command": "test" }
    }
  ]
}
```

**Contoh plan — restart perangkat (sudah dikonfirmasi user):**
```json
{
  "plan": [
    {
      "step": 1,
      "action": "function_two",
      "parameters": { "id": "98asdikuqhweuiq", "command": "RESET#" }
    }
  ]
}
```

---

## Contoh Plan Multi-Step

**Contoh — pelanggan sebut param1:**
```json
{
  "plan": [
    {
      "step": 1,
      "action": "function_one",
      "parameters": { "param1": "1231ws12" }
    }
  ]
}
```

**Contoh — pelanggan sebut nomor param2:**
```json
{
  "plan": [
    {
      "step": 1,
      "action": "function_one",
      "parameters": { "param2": "asdaaakjhak" }
    }
  ]
}
```
