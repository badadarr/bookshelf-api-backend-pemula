# Copilot Instructions — Bookshelf API

## Gambaran Proyek

Membangun **Bookshelf API** — RESTful API untuk manajemen data buku menggunakan **Node.js** dengan framework **Hapi** atau **Express**.

---

## Stack & Ketentuan Teknis

- **Framework:** Hapi.js atau Express.js (selain itu akan ditolak)
- **Port:** `9000`
- **Module ID generator:** `nanoid` — jika pakai CommonJS, install versi 3: `npm install nanoid@3`
- **Runner script:**
  ```json
  "scripts": {
    "start": "node src/server.js",
    "start-dev": "nodemon src/server.js"
  }
  ```
  > `npm run start` **tidak boleh** menggunakan nodemon

---

## Struktur Data Buku

Setiap objek buku yang disimpan di server harus memiliki struktur berikut:

```json
{
  "id": "Qbax5Oy7L8WKf74l",
  "name": "Buku A",
  "year": 2010,
  "author": "John Doe",
  "summary": "Lorem ipsum dolor sit amet",
  "publisher": "Dicoding Indonesia",
  "pageCount": 100,
  "readPage": 25,
  "finished": false,
  "reading": false,
  "insertedAt": "2021-03-04T09:11:44.598Z",
  "updatedAt": "2021-03-04T09:11:44.598Z"
}
```

**Properti yang digenerate di server:**
| Properti | Keterangan |
|---|---|
| `id` | Unik, generate dengan `nanoid` |
| `finished` | `true` jika `pageCount === readPage` |
| `insertedAt` | `new Date().toISOString()` saat buku ditambahkan |
| `updatedAt` | Sama dengan `insertedAt` saat insert; diperbarui saat update |

---

## API Endpoints

### POST `/books` — Tambah Buku

**Request Body:**
```json
{
  "name": "string",
  "year": "number",
  "author": "string",
  "summary": "string",
  "publisher": "string",
  "pageCount": "number",
  "readPage": "number",
  "reading": "boolean"
}
```

**Response sukses — 201:**
```json
{
  "status": "success",
  "message": "Buku berhasil ditambahkan",
  "data": { "bookId": "1L7ZtDUFeGs7VlEt" }
}
```

**Response gagal — 400 (name kosong):**
```json
{ "status": "fail", "message": "Gagal menambahkan buku. Mohon isi nama buku" }
```

**Response gagal — 400 (readPage > pageCount):**
```json
{ "status": "fail", "message": "Gagal menambahkan buku. readPage tidak boleh lebih besar dari pageCount" }
```

---

### GET `/books` — Tampilkan Semua Buku

**Response sukses — 200:**
```json
{
  "status": "success",
  "data": {
    "books": [
      { "id": "Qbax5Oy7L8WKf74l", "name": "Buku A", "publisher": "Dicoding Indonesia" }
    ]
  }
}
```
> Jika kosong, kembalikan array `books: []`

**Query Parameters (Opsional):**
| Parameter | Keterangan |
|---|---|
| `?name=` | Filter buku yang mengandung nama (non-case sensitive) |
| `?reading=0\|1` | `0` = tidak sedang dibaca, `1` = sedang dibaca |
| `?finished=0\|1` | `0` = belum selesai, `1` = sudah selesai |

---

### GET `/books/{bookId}` — Detail Buku

**Response sukses — 200:**
```json
{
  "status": "success",
  "data": { "book": { "...seluruh properti buku..." } }
}
```

**Response gagal — 404:**
```json
{ "status": "fail", "message": "Buku tidak ditemukan" }
```

---

### PUT `/books/{bookId}` — Update Buku

**Request Body:** sama dengan POST

**Response sukses — 200:**
```json
{ "status": "success", "message": "Buku berhasil diperbarui" }
```

**Response gagal — 400 (name kosong):**
```json
{ "status": "fail", "message": "Gagal memperbarui buku. Mohon isi nama buku" }
```

**Response gagal — 400 (readPage > pageCount):**
```json
{ "status": "fail", "message": "Gagal memperbarui buku. readPage tidak boleh lebih besar dari pageCount" }
```

**Response gagal — 404 (id tidak ditemukan):**
```json
{ "status": "fail", "message": "Gagal memperbarui buku. Id tidak ditemukan" }
```

---

### DELETE `/books/{bookId}` — Hapus Buku

**Response sukses — 200:**
```json
{ "status": "success", "message": "Buku berhasil dihapus" }
```

**Response gagal — 404:**
```json
{ "status": "fail", "message": "Buku gagal dihapus. Id tidak ditemukan" }
```

---

## Checklist Wajib (Mandatory)

- [ ] Server berjalan di port `9000`
- [ ] `npm run start` menggunakan `node`, bukan `nodemon`
- [ ] POST `/books` — menyimpan buku dengan struktur lengkap
- [ ] GET `/books` — menampilkan semua buku (hanya `id`, `name`, `publisher`)
- [ ] GET `/books/{bookId}` — menampilkan detail buku
- [ ] PUT `/books/{bookId}` — mengupdate buku berdasarkan id
- [ ] DELETE `/books/{bookId}` — menghapus buku berdasarkan id
- [ ] Validasi `name` wajib diisi pada POST dan PUT
- [ ] Validasi `readPage` tidak boleh lebih besar dari `pageCount`
- [ ] `finished` dihitung otomatis dari `pageCount === readPage`

## Checklist Opsional (Nilai Tinggi)

- [ ] Query parameter `?name=` pada GET `/books`
- [ ] Query parameter `?reading=` pada GET `/books`
- [ ] Query parameter `?finished=` pada GET `/books`
- [ ] Setup ESLint dengan salah satu style guide (Airbnb / Google / Standard)
- [ ] Tidak ada error saat menjalankan `npx eslint .`
