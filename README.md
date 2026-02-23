# instalasi-web-panel

## 🛠️ Teknologi yang Digunakan
- **Backend:** Python/PHP/Node.js
- **Framework:** Laravel 12
- **Database:** MySQL
- **Server:** Nginx
- **OS:** Ubuntu 22.04 LTS

## 📥 Panduan Instalasi di Server
Ikuti langkah-langkah di bawah ini untuk menyiapkan lingkungan server.

### 1. Update Sistem
Pertama, pastikan sistem Anda dalam keadaan terbaru:
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. install nginx
```bash
sudo apt install nginx
```
### 3. install PHP
Karena Anda menggunakan Nginx, kita tidak boleh menginstal paket php biasa (yang memicu instalasi Apache). Kita harus menginstal PHP-FPM (FastCGI Process Manager).
Untuk versi yang paling stabil dan didukung penuh saat ini di lingkungan Ubuntu adalah PHP 8.3. (PHP 8.4 baru saja rilis dan sangat stabil, namun PHP 8.3 memiliki kompatibilitas modul yang lebih luas untuk saat ini).
Berikut adalah langkah-langkah instalasinya:
1. Tambahkan Repository PHP (Ondřej Surý)
   Agar mendapatkan versi PHP yang paling update dan stabil di Ubuntu, gunakan repository PPA yang dikelola oleh pengembang PHP resmi untuk Debian/Ubuntu:
   ```bash
      sudo add-apt-repository ppa:ondrej/php -y
      sudo apt update
    ```
3. Instal PHP 8.3-FPM dan Ekstensi Penting
4. Pastikan Layanan PHP-FPM Berjalan
5. Konfigurasi Nginx agar bisa membaca PHP
6. Test dan Restart Nginx
7. Uji Coba 
