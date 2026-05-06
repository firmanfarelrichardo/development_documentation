# Dokumentasi Infrastruktur Terdistribusi — Zeta.co.id

Proyek ini merupakan implementasi arsitektur jaringan **microservices terdistribusi** yang dibangun untuk memenuhi **Tugas 1 dan Tugas 2 Virtualisasi & Cloud Computing**.  
Infrastruktur memanfaatkan jaringan virtual **VPN Tailscale** untuk menghubungkan tiga peladen (*server*) yang berjalan pada lingkungan berbeda secara aman dan terenkripsi.

---

# 👥 Tim Pengembang

| Peran | Nama Anggota | NPM | Tanggung Jawab Teknis |
|--------|-------------|-----|-----------------------|
| **Anggota 1** | Firman Farel Richardo | 2315061099 | DNS Server (Linux + VestaCP) |
| **Anggota 2** | Muhammad Farhan | 2315061083 | Web Server (Linux + Apache + PHP) |
| **Anggota 3** | Arza Restu Arjuna | 2315061051 | Database Server (Linux + MySQL/PgSQL) |

---

# 🏗️ Topologi & Arsitektur Jaringan

Ketiga server dijalankan pada mesin virtual (**VirtualBox**) lokal masing-masing anggota.  
Karena tiap server berada di jaringan/lokasi berbeda dan menggunakan NAT, digunakan **Tailscale** sebagai solusi **Overlay Mesh VPN**.

Dengan Tailscale:

- Seluruh server dapat saling berkomunikasi secara privat
- Tidak memerlukan konfigurasi **port forwarding**
- Koneksi terenkripsi secara **end-to-end**
- Mendapatkan IP privat statis jaringan VPN (`100.x.x.x`)

---

## 🌐 Struktur Domain Internal

| Fungsi | Domain/Subdomain |
|--------|------------------|
| Domain Utama | `zeta.co.id` |
| Web Server | `web.zeta.co.id` / `www.zeta.co.id` |
| Database Server | `db.zeta.co.id` |

---

# 🔐 Interkoneksi VPN Menggunakan Tailscale

## Mekanisme Implementasi

1. Setiap mesin virtual Linux tergabung ke jaringan **Tailnet** menggunakan **Auth Keys**
2. Masing-masing server memperoleh **Private VPN IP** (`100.x.x.x`)
3. Komunikasi antar server dilakukan melalui jalur VPN privat
4. Web Server mengakses Database Server melalui IP VPN internal

---

# 🌍 Konfigurasi DNS Server

Tahap kedua difokuskan pada aktivasi DNS dan konfigurasi zona domain menggunakan:

- **DNS Server:** Linux + VestaCP  
- **Administrator DNS:** Anggota 1  

DNS Server bertindak sebagai **otoritas zona** untuk domain internal:

```bash
zeta.co.id
```

---

# 📋 DNS Resource Records

Konfigurasi record pada zona domain `zeta.co.id`:

| Tipe Record | Host / Nama | Value / Destination | Deskripsi |
|-------------|-------------|---------------------|-----------|
| **A Record** | `web` | `<IP_Tailscale_Anggota_2>` | Mengarahkan `web.zeta.co.id` ke Web Server |
| **A Record** | `db` | `<IP_Tailscale_Anggota_3>` | Mengarahkan `db.zeta.co.id` ke Database Server |
| **CNAME** | `www` | `web.zeta.co.id` | Alias domain web |
| **NS** | `@` | `ns1.zeta.co.id` | Name Server otoritatif domain |
| **SOA** | `@` | Default VestaCP | Informasi otoritas zona DNS |
| **MX** | `@` | `mail.zeta.co.id` *(opsional)* | Routing email domain |

---

# 🧪 Validasi Sistem DNS

Pengujian dilakukan menggunakan tools:

- **DIG** (Linux)
- **NSLOOKUP** (Windows)

---

## 1️⃣ Validasi Subdomain Web Server

### Command

```cmd
nslookup web.zeta.co.id 100.124.206.35
```

### Expected Result

```cmd
Address: 100.68.87.21
```

Menunjukkan bahwa subdomain `web.zeta.co.id` berhasil dipetakan ke Web Server.

---

## 2️⃣ Validasi Subdomain Database Server

### Command

```bash
dig db.zeta.co.id @100.124.206.35
```

### Expected Result

Pada bagian:

```bash
ANSWER SECTION
```

akan muncul:

```bash
db.zeta.co.id.   IN   A   100.110.81.10
```

Menunjukkan DNS berhasil memetakan Database Server dengan benar.

---

# 🎥 Dokumentasi Video Demonstrasi

Video dokumentasi proses deployment, konfigurasi DNS, serta validasi query DNS dapat diakses pada tautan berikut:

👉 **[MASUKKAN LINK YOUTUBE DI SINI]**

---

# 🛠️ Teknologi yang Digunakan

| Komponen | Teknologi |
|---------|-----------|
| Virtualization | VirtualBox |
| VPN Overlay Network | Tailscale |
| DNS Management | VestaCP |
| Web Server | Apache + PHP |
| Database Server | MySQL / PostgreSQL |
| Operating System | Linux (Ubuntu/Debian/CentOS) |

---

# 📌 Catatan Implementasi

- Infrastruktur ini berjalan sepenuhnya pada jaringan privat internal VPN
- Domain hanya dapat di-resolve oleh client yang terhubung ke Tailnet / DNS internal
- Implementasi ini mensimulasikan arsitektur distributed server environment sederhana

---

# ✅ Status Implementasi

- [x] Deployment DNS Server  
- [x] Deployment Web Server  
- [x] Deployment Database Server  
- [x] Integrasi VPN Tailscale  
- [x] Konfigurasi DNS Resource Records  
- [x] Validasi Query DNS  
- [x] Dokumentasi Video Demonstrasi  

---

# 📖 Lisensi

Dokumentasi ini dibuat untuk keperluan akademik mata kuliah **Virtualisasi & Cloud Computing**.
