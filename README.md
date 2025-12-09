# Laporan Rekonaisans Keamanan Siber  
**Target:** Ekosistem layanan Pemerintah Kota Makassar  
**Pendekatan:** Passive & Active Reconnaissance (lab terisolasi)

---

## 1. Ruang Lingkup

- Passive reconnaissance terhadap domain publik `makassarkota.go.id` beserta subdomain terkait menggunakan sumber informasi terbuka (OSINT) tanpa pengiriman paket probing ke server produksi.  
- Active reconnaissance dilakukan terbatas pada lingkungan lab terhadap host **10.39.111.38** sebagai simulasi server layanan publik.

---

## 2. Passive Reconnaissance

Aktivitas OSINT yang dilakukan mencakup:

- Enumerasi subdomain dan sertifikat TLS melalui layanan pencarian sertifikat (crt.sh) dan sejenisnya.  
- Identifikasi teknologi web (OS, web server, CMS, plugin, CDN) menggunakan platform pemetaan teknologi seperti BuiltWith.  
- Observasi portal layanan, halaman login, helpdesk, dan dokumentasi publik (misalnya profil pegawai dan struktur organisasi) untuk memetakan pemangku kepentingan dan jejak digital.  

**Tujuan:** Mengumpulkan informasi arsitektur, teknologi, dan konteks organisasi tanpa menimbulkan trafik berisiko ke sistem produksi.

---

## 3. Active Reconnaissance (Lab 10.39.111.38)

Seluruh aktivitas di bawah ini hanya dilakukan pada mesin lab:

- **Host Discovery**  
  - Menentukan keberadaan host aktif dalam segmen jaringan penyerang.

- **TCP SYN Scan (`-sS`)**  
  - Mengidentifikasi port TCP terbuka pada host 10.39.111.38.  
  - Ditemukan port 22 (SSH), 80 (HTTP), dan 6667 (IRC) dalam keadaan `open`.

- **UDP Scan (`-sU`)**  
  - Menguji port UDP populer; tidak ditemukan layanan UDP yang terbuka pada daftar top ports.

- **Service & Version Detection (`-sV`)**  
  - Port 22: OpenSSH 6.6.1p1 (Ubuntu).  
  - Port 80: Apache httpd 2.4.7.  
  - Port 6667: Layanan IRC generik.

- **OS Fingerprinting (`-O`)**  
  - Sistem operasi teridentifikasi sebagai Linux dengan kernel generasi **3.x – 4.x** (general purpose server).

- **Analisis Paket dengan Wireshark**  
  - Mengamati rangkaian paket SYN → SYN-ACK → RST yang mengonfirmasi teknik TCP SYN Scan (half-open scan).  

---

## 4. Temuan Kunci

1. **Paparan Informasi Publik (Passive Reconnaissance)**  
   - Jejak teknologi, subdomain, dan kontak organisasi dapat dipetakan hanya dari sumber publik; informasi ini berpotensi dimanfaatkan untuk social engineering dan pemilihan target serangan.

2. **Layanan Kritis dengan Versi Usang (Active Reconnaissance)**  
   - Versi OpenSSH 6.6.1p1 dan Apache 2.4.7 yang terdeteksi pada host lab merepresentasikan skenario server dengan software yang telah lama, berpotensi mengandung CVE yang sudah terdokumentasi.  

3. **Port IRC 6667 sebagai Anomali**  
   - Keberadaan port 6667/tcp (IRC) pada server umum sering dikaitkan dengan kanal Command & Control (C2) botnet atau backdoor jika tidak diawasi secara ketat.

4. **Kernel Linux Lama (3.x – 4.x)**  
   - OS dengan kernel generasi lama cenderung berada di luar masa dukungan penuh sehingga berisiko terhadap eksploitasi privilege escalation maupun kerentanan lokal lainnya.

5. **Konfirmasi Metode SYN Scan**  
   - Capture Wireshark menunjukkan pola half-open scanning yang efektif memetakan port terbuka sambil meminimalkan tercatatnya koneksi penuh di sisi aplikasi target.

---

## 5. Rekomendasi Remediasi

1. **Manajemen Aset Digital (OSINT / Exposure)**  
   - Privatisasi atau pembatasan repository publik yang berisi kode/skenario terkait infrastruktur pemerintah.  
   - Peningkatan security awareness bagi pegawai yang alamat emailnya dipublikasikan untuk mengurangi risiko phishing dan spear phishing.

2. **Patch Management & Hardening**  
   - Upgrade sistem operasi dan layanan (SSH, HTTP) ke versi yang masih didukung vendor.  
   - Menonaktifkan atau membatasi layanan IRC pada port 6667 bila tidak digunakan secara resmi.

3. **Pengamanan Jaringan**  
   - Penerapan firewall host / perimeter untuk membatasi akses ke port 22, 80, dan 443 hanya dari alamat IP terotorisasi.  
   - Implementasi IDS/IPS untuk mendeteksi pola pemindaian aktif, termasuk signature TCP SYN Scan, secara real-time.

---

## 6. Catatan Etis

- Seluruh pemindaian aktif dilakukan **hanya** terhadap mesin lab dengan IP 10.39.111.38 di lingkungan terkontrol.  
- Untuk sistem produksi pemerintah, aktivitas terbatas pada pengintaian pasif berbasis OSINT tanpa interaksi langsung yang intrusif terhadap server.

---
## LINK DOKUMENTASI
https://youtu.be/uvYjNhELLVg
