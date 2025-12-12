

## 1\. File Utama (Root Repository)

File ini akan menjadi halaman utama repositori GitHub Anda.

### `README.md`

````markdown
# Dokumentasi Sistem Inventori Berbasis GCP

Selamat datang di dokumentasi proyek Sistem Inventori. Proyek ini dibangun sebagai solusi manajemen stok dengan arsitektur mikroservis menggunakan Google Cloud Platform (GCP).

## 🔗 Akses Aplikasi

| Komponen | URL | Catatan |
| :--- | :--- | :--- |
| **Frontend (Web UI)** | [Inventori Login](https://inventori-frontend-894858667370.us-central1.run.app/login) | Antarmuka pengguna untuk interaksi harian. |
| **Backend (API Base URL)** | [Inventori API](https://inventori-backend-894858667370.us-central1.run.app/api) | Layanan RESTful API untuk pemrosesan data. |

## 📚 Daftar Isi Dokumentasi

Dokumentasi lengkap dibagi ke dalam beberapa bagian utama:

1.  [**Pendahuluan**](docs/01-Pendahuluan.md): Ringkasan proyek dan teknologi yang digunakan.
2.  [**Arsitektur Teknis dan GCP**](docs/02-Arsitektur-GCP.md): Detail mengenai layanan GCP dan diagram arsitektur.
3.  [**Dokumentasi API**](docs/03-Dokumentasi-API.md): Spesifikasi lengkap semua endpoint API.
4.  [**Panduan Pengguna**](docs/04-Panduan-Pengguna.md): Alur penggunaan aplikasi dari sisi pengguna akhir.
5.  [**Panduan Deployment**](docs/05-Panduan-Deployment.md): Langkah-langkah untuk membangun dan mendeploy aplikasi.

---

## 2. Struktur Folder `/docs`

Buat folder bernama `docs` di *root* repositori, dan masukkan file-file berikut di dalamnya.

### 📄 `docs/01-Pendahuluan.md`

```markdown
# 01. Pendahuluan

## 1.1 Ringkasan Proyek

Sistem Inventori adalah aplikasi manajemen stok berbasis web yang dirancang untuk membantu perusahaan dalam melacak, mengelola, dan memantau ketersediaan barang secara real-time.

**Tujuan Utama:**
* Mencatat transaksi barang masuk (restock).
* Mencatat transaksi barang keluar (penjualan/penggunaan).
* Memberikan visibilitas status stok secara akurat.

## 1.2 Teknologi Utama

Aplikasi ini mengadopsi arsitektur *cloud-native* dengan memanfaatkan layanan *serverless* dari Google Cloud Platform.

| Komponen | Teknologi/Layanan | Fungsi |
| :--- | :--- | :--- |
| **Penyedia Cloud** | Google Cloud Platform (GCP) | Platform hosting utama. |
| **Komputasi (Hosting)** | Google Cloud Run | Menjalankan *frontend* dan *backend* sebagai container. |
| **Database** | [Cloud SQL for PostgreSQL/MySQL/Firestore] | Penyimpanan data inventori utama. |
| **Backend Framework** | [Contoh: Node.js/Express, Python/Flask] | Logika bisnis dan layanan RESTful API. |
| **Frontend Framework** | [Contoh: React, Vue.js, Vanilla JS] | Antarmuka pengguna interaktif. |
````

### 📄 `docs/02-Arsitektur-GCP.md`

```markdown
# 02. Arsitektur Teknis dan GCP

## 2.1 Diagram Arsitektur

**[GAMBAR DIAGRAM ARSITEKTUR DI SINI]**

## 2.2 Komponen Layanan GCP

Proyek ini dibangun menggunakan model arsitektur mikroservis yang di-deploy sebagai layanan terkelola di GCP.

| Layanan GCP | Fungsi dalam Arsitektur | Detail Konfigurasi |
| :--- | :--- | :--- |
| **Cloud Run (inventori-frontend)** | Menyajikan antarmuka web (UI) kepada pengguna. | Region: `us-central1`, Alokasi Memori: [X]GB. |
| **Cloud Run (inventori-backend)** | Menjalankan server RESTful API. | Region: `us-central1`, Koneksi ke Cloud SQL melalui konektor VPC. |
| **Cloud SQL / Firestore** | Basis Data. Menjadi sumber kebenaran (Source of Truth) untuk semua data inventori dan transaksi. | [Sebutkan jenis DB, misalnya: PostgreSQL 14], Mode: [High Availability/Standalone]. |
| **VPC (Virtual Private Cloud)** | Digunakan untuk membuat jaringan internal pribadi, memastikan komunikasi yang aman antara Cloud Run Backend dan Cloud SQL. | |
| **Cloud IAM** | Mengelola *Service Account* yang digunakan oleh layanan Cloud Run untuk mengakses sumber daya lain (misalnya, izin untuk koneksi ke Cloud SQL). | |
| **Cloud Logging** | Mengumpulkan semua log dari layanan Cloud Run untuk pemecahan masalah dan monitoring. | |

## 2.3 Aliran Data

1.  Pengguna mengakses **Cloud Run Frontend** melalui URL publik.
2.  Frontend memuat UI dan membuat permintaan ke **Cloud Run Backend** (API).
3.  Backend memvalidasi permintaan dan token otorisasi.
4.  Backend terhubung secara aman ke **Cloud SQL** melalui **Konektor VPC** untuk memproses data (SELECT, INSERT, UPDATE).
5.  Data dikembalikan sebagai JSON melalui API ke Frontend, dan ditampilkan kepada pengguna.
```

### 📄 `docs/03-Dokumentasi-API.md`

*(Ini adalah konten yang telah kita buat sebelumnya, diulang untuk kelengkapan)*

````markdown
# 03. Dokumentasi RESTful API

Dokumentasi ini menjelaskan endpoint yang tersedia pada layanan backend, yang berjalan pada Google Cloud Run.

**Base URL:** `https://inventori-backend-894858667370.us-central1.run.app/api`

---

## 1. Otentikasi (Auth)

Semua endpoint kecuali `/api/auth/login` dan `/api/auth/register` (jika ada) membutuhkan token otorisasi.

### 1.1 Login Pengguna

| Detail | Deskripsi |
| :--- | :--- |
| **Endpoint** | `/api/auth/login` |
| **Metode** | `POST` |
| **Deskripsi** | Digunakan untuk otentikasi pengguna dan mendapatkan **JSON Web Token (JWT)**. |

**Request Body (JSON):**
```json
{
  "username": "nama_pengguna",
  "password": "kata_sandi"
}
````

**Response Sukses (200 OK):**

```json
{
  "message": "Login berhasil",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoiMTIzNDUifQ.SflKxwR",
  "user": {
    "id": "12345",
    "username": "nama_pengguna"
  }
}
```

> **Header Otorisasi:** Gunakan token yang didapat pada setiap request terproteksi dengan skema **Bearer Token**.
> `Authorization: Bearer <TOKEN_JWT_ANDA>`

-----

## 2\. Manajemen Barang (Items)

### 2.1 Mendapatkan Semua Barang

| Detail | Deskripsi |
| :--- | :--- |
| **Endpoint** | `/api/items` |
| **Metode** | `GET` |
| **Deskripsi** | Mengambil daftar semua barang inventori. |
| **Otorisasi** | Ya (Bearer Token) |

**Parameter Query (Opsional):**

| Parameter | Tipe | Deskripsi |
| :--- | :--- | :--- |
| `limit` | Integer | Batas jumlah barang yang ditampilkan per halaman. |
| `page` | Integer | Nomor halaman yang ingin diambil. |

**Response Sukses (200 OK):**

```json
[
  {
    "id": "item-001",
    "nama": "Mouse Wireless Logitech",
    "sku": "LGT-M01",
    "stok": 50,
    "unit": "pcs",
    "harga": 150000
  },
  // ... barang lainnya
]
```

### 2.2 Menambah Barang Baru

| Detail | Deskripsi |
| :--- | :--- |
| **Endpoint** | `/api/items` |
| **Metode** | `POST` |
| **Deskripsi** | Menambahkan entri barang baru ke dalam inventori. |
| **Otorisasi** | Ya (Bearer Token) |

**Request Body (JSON):**

```json
{
  "nama": "Monitor LED 24 Inci",
  "sku": "SAMS-M24",
  "stok": 25,
  "unit": "unit",
  "harga": 2200000
}
```

**Response Sukses (201 Created):**
Mengembalikan objek barang yang baru dibuat beserta ID-nya.

-----

## 3\. Manajemen Transaksi (Transactions)

### 3.1 Mencatat Transaksi Barang

| Detail | Deskripsi |
| :--- | :--- |
| **Endpoint** | `/api/transactions` |
| **Metode** | `POST` |
| **Deskripsi** | Mencatat transaksi **masuk** (restock) atau **keluar** (penjualan/penggunaan) dan secara otomatis memperbarui stok barang. |
| **Otorisasi** | Ya (Bearer Token) |

**Request Body (JSON):**

```json
{
  "item_id": "item-001",
  "jumlah": 5,
  "tipe": "keluar", // Nilai harus 'masuk' atau 'keluar'
  "keterangan": "Penjualan ke Toko Bintang"
}
```

**Response Sukses (201 Created):**

```json
{
  "id": "trx-005",
  "item_id": "item-001",
  "jumlah": 5,
  "tipe": "keluar",
  "stok_sebelum": 50,
  "stok_sesudah": 45,
  "tanggal": "2025-12-12T14:00:00Z"
}
```

````

### 📄 `docs/04-Panduan-Pengguna.md`

```markdown
# 04. Panduan Pengguna (Frontend)

Panduan ini menjelaskan langkah-langkah dasar penggunaan aplikasi melalui antarmuka web (Frontend).

**URL Aplikasi:** [https://inventori-frontend-894858667370.us-central1.run.app/login](https://inventori-frontend-894858667370.us-central1.run.app/login)

## 1. Login dan Dashboard

1.  Akses URL aplikasi.
2.  Masukkan **Username** dan **Password** yang valid, lalu klik tombol **Login**.
3.  Setelah berhasil, Anda akan diarahkan ke halaman **Dashboard**.
    * **Dashboard** menampilkan ringkasan data penting, seperti Total Stok, Jumlah Barang, dan Notifikasi Stok Rendah.

## 2. Manajemen Barang

Bagian ini digunakan untuk menambah, melihat, dan mengubah detail barang inventori.

### 2.1 Menambah Barang

1.  Navigasi ke menu **Barang** atau **Item**.
2.  Klik tombol **Tambah Baru**.
3.  Isi formulir dengan detail barang:
    * **Nama Barang:** (misalnya: Flashdisk 64GB)
    * **SKU (Stock Keeping Unit):** (misalnya: FDK-64)
    * **Stok Awal:** (Angka, misalnya: 100)
    * **Harga Satuan:** (Angka, misalnya: 75000)
4.  Klik **Simpan**.

### 2.2 Mengubah Stok

Stok tidak boleh diubah langsung. Stok harus diperbarui melalui **Transaksi**.

## 3. Mencatat Transaksi (Stok Masuk/Keluar)

### 3.1 Transaksi Masuk (Restock)

1.  Navigasi ke menu **Transaksi**.
2.  Klik tombol **Tambah Transaksi**.
3.  Pilih **Tipe Transaksi** sebagai **MASUK**.
4.  Pilih **Nama Barang** yang di-restock.
5.  Masukkan **Jumlah** barang yang masuk.
6.  Tambahkan **Keterangan** (misalnya: Pembelian dari Supplier A).
7.  Klik **Proses Transaksi**. Stok barang akan bertambah secara otomatis.

### 3.2 Transaksi Keluar (Penjualan)

1.  Navigasi ke menu **Transaksi**.
2.  Klik tombol **Tambah Transaksi**.
3.  Pilih **Tipe Transaksi** sebagai **KELUAR**.
4.  Pilih **Nama Barang** yang terjual/digunakan.
5.  Masukkan **Jumlah** barang yang keluar.
6.  Tambahkan **Keterangan** (misalnya: Penjualan ke Pelanggan Ritel).
7.  Klik **Proses Transaksi**. Stok barang akan berkurang secara otomatis.
````

### 📄 `docs/05-Panduan-Deployment.md`

````markdown
# 05. Panduan Deployment dan Operasional

Dokumen ini berisi langkah-langkah untuk membangun (build) dan mendeploy aplikasi ke Google Cloud Run.

## 1. Prasyarat

Sebelum memulai, pastikan Anda telah menginstal dan mengkonfigurasi yang berikut:

* Akun Google Cloud aktif dengan tagihan (billing) diaktifkan.
* Google Cloud SDK (`gcloud`) terinstal dan terotentikasi.
* Docker Engine terinstal.

## 2. Konfigurasi Awal GCP

### 2.1 Mengaktifkan API

Pastikan API berikut sudah diaktifkan di GCP Console:
* Cloud Run API
* Cloud Build API
* Cloud SQL Admin API

### 2.2 Konfigurasi Cloud SQL

Pastikan instance Cloud SQL Anda sudah berjalan dan memiliki basis data serta akun pengguna aplikasi yang siap.

## 3. Deployment Backend (API)

Asumsikan file `Dockerfile` berada di root folder backend.

### 3.1 Build dan Push Image Container

```bash
# Ganti [GCP_PROJECT_ID] dengan ID proyek GCP Anda
PROJECT_ID="[GCP_PROJECT_ID]"
IMAGE_NAME="inventori-backend"
REGION="us-central1"

# 1. Build image Docker
docker build -t gcr.io/${PROJECT_ID}/${IMAGE_NAME}:latest .

# 2. Push image ke Google Container Registry (GCR)
docker push gcr.io/${PROJECT_ID}/${IMAGE_NAME}:latest
````

### 3.2 Deploy ke Cloud Run

Deploy layanan backend ke Cloud Run, pastikan koneksi ke Cloud SQL diatur dengan benar (jika menggunakan konektor VPC).

```bash
gcloud run deploy ${IMAGE_NAME} \
  --image gcr.io/${PROJECT_ID}/${IMAGE_NAME}:latest \
  --platform managed \
  --region ${REGION} \
  --allow-unauthenticated \
  --set-env-vars [DAFTAR ENVIRONMENT VARIABLES DB/KONFIGURASI] 
  # Contoh: --set-env-vars DB_HOST=..., DB_USER=...
```

## 4\. Deployment Frontend (UI)

Ulangi langkah 3.1 dan 3.2 untuk proyek frontend Anda.

```bash
# Ganti [GCP_PROJECT_ID] dengan ID proyek GCP Anda
PROJECT_ID="[GCP_PROJECT_ID]"
IMAGE_NAME="inventori-frontend"
REGION="us-central1"

# 1. Build image Docker Frontend
# (Pindah ke direktori frontend sebelum menjalankan build)
docker build -t gcr.io/${PROJECT_ID}/${IMAGE_NAME}:latest .

# 2. Push image ke GCR
docker push gcr.io/${PROJECT_ID}/${IMAGE_NAME}:latest

# 3. Deploy ke Cloud Run
gcloud run deploy ${IMAGE_NAME} \
  --image gcr.io/${PROJECT_ID}/${IMAGE_NAME}:latest \
  --platform managed \
  --region ${REGION} \
  --allow-unauthenticated 
  # CATATAN: Pastikan environment variable BASE_API_URL mengarah ke backend!
  # Contoh: --set-env-vars VITE_API_BASE_URL=https://inventori-backend-...
```

## 5\. Operasional dan Pemecahan Masalah

### 5.1 Monitoring dan Log

Semua log aplikasi (output konsol dari backend dan frontend) dapat diakses melalui **Cloud Logging** di GCP Console.

  * Gunakan filter `resource.type="cloud_run_revision"` dan `resource.labels.service_name="inventori-backend"` untuk melihat log API.

### 5.2 Skalabilitas

Cloud Run secara otomatis menskalakan layanan hingga [X] instance (sesuai konfigurasi Anda) saat traffic meningkat, dan menskalakan hingga nol (0) saat tidak ada traffic.

```
```
