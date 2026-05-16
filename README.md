# lapakID — Marketplace ID Premium

Platform jual beli ID premium dengan MongoDB backend, sistem like anti-spam, promo code, dan dashboard admin.

---

## Struktur Proyek

```
lapakid/
├── api/
│   └── index.js          ← Backend API (Node.js, Vercel serverless)
├── public/
│   ├── index.html        ← Halaman utama (listing ID, popular, like)
│   ├── payment.html      ← Halaman pembayaran & promo
│   ├── about.html        ← Info toko, garansi, tier, FAQ
│   └── dashboard.html    ← Admin panel
├── vercel.json           ← Konfigurasi Vercel
├── package.json
└── .env.example          ← Template environment variables
```

---

## Setup & Deploy ke Vercel

### 1. Clone / Upload proyek
Upload semua file ke repository GitHub.

### 2. Hubungkan ke Vercel
- Buka [vercel.com](https://vercel.com) → New Project → Import repo
- Framework: **Other**

### 3. Set Environment Variables di Vercel
Buka Settings → Environment Variables, tambahkan:

| Key | Value |
|---|---|
| `MONGODB_URI` | `mongodb+srv://n4taza_db:...@cluster0.pdfnlfb.mongodb.net/` |
| `ADMIN_TOKEN` | Token rahasia admin kamu (buat string acak yang kuat) |

### 4. Deploy
Klik Deploy — selesai!

---

## Database MongoDB (Auto-init)

Koleksi akan dibuat otomatis saat API pertama kali dipanggil:

| Koleksi | Isi |
|---|---|
| `ids` | Data ID (number, tier, sold, likes, addedAt, note) |
| `likes` | Log like per IP per ID (anti-duplikat) |
| `bans` | IP yang diblokir karena spam like |
| `promos` | Kode promo (discount, maxUses, expiresAt) |
| `payments` | Data pesanan pembayaran |
| `settings` | Harga tier & biaya admin |

---

## API Endpoints

### Public
| Method | URL | Keterangan |
|---|---|---|
| GET | `/api/ids` | Ambil semua ID |
| GET | `/api/ids/popular` | ID dengan likes terbanyak |
| GET | `/api/ids/stats` | Statistik total ID |
| GET | `/api/ids/:number` | Detail satu ID |
| POST | `/api/like/:number` | Like sebuah ID (1 IP 1 like, anti-spam) |
| GET | `/api/like/check/:number` | Cek apakah IP sudah like |
| POST | `/api/promo/validate` | Validasi kode promo |
| GET | `/api/settings` | Ambil harga & setting |
| POST | `/api/payment` | Buat pesanan |

### Admin (Header: `x-admin-token: TOKEN`)
| Method | URL | Keterangan |
|---|---|---|
| POST | `/api/admin/login` | Login admin |
| POST | `/api/ids` | Tambah ID |
| POST | `/api/ids/bulk` | Tambah ID massal |
| PUT | `/api/ids/:number` | Update ID |
| DELETE | `/api/ids/:number` | Hapus ID |
| GET | `/api/payments` | Lihat semua pembayaran |
| PUT | `/api/payments/:id/confirm` | Konfirmasi pembayaran |
| GET | `/api/promos` | Lihat semua promo |
| POST | `/api/promos` | Buat promo |
| PUT | `/api/promos/:id` | Update promo |
| DELETE | `/api/promos/:id` | Hapus promo |
| PUT | `/api/settings/:key` | Update setting |
| GET | `/api/bans` | Lihat IP banned |
| DELETE | `/api/bans/:ip` | Unban IP |

---

## Sistem Anti-Spam Like

- 1 IP hanya bisa like 1 ID sekali
- Jika 1 IP melakukan lebih dari 10 like dalam 5 menit → IP otomatis diblokir
- Admin bisa unban IP dari dashboard

---

## Login Admin

Buka `/dashboard.html` → masukkan `ADMIN_TOKEN` yang sudah kamu set di Vercel.

---

## Tier & Harga Default

| Tier | Harga Default |
|---|---|
| Low | Rp 125.000 |
| Medium | Rp 450.000 |
| High | Rp 850.000 |
| Legend | Rp 1.350.000 |

Harga bisa diubah dari dashboard admin → tab Pengaturan.

---

## Promo Code

- Diskon max: **88%**
- Bisa diset batas penggunaan (maxUses) dan waktu kadaluarsa
- Dashboard admin otomatis preview harga setelah diskon saat kamu input persentase
