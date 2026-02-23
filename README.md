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
   
2. Instal PHP 8.3-FPM dan Ekstensi Penting
   Gunakan perintah ini untuk menginstal PHP yang dikhususkan untuk Nginx dan MySQL, serta ekstensi yang butuhkan (mysqli, zip, fpm):

   ```bash
   sudo apt install -y php8.3-fpm php8.3-mysql php8.3-common php8.3-cli php8.3-zip php8.3-gd php8.3-mbstring php8.3-curl php8.3-xml
    ```
   
3. Pastikan Layanan PHP-FPM Berjalan
   Setelah instalasi selesai, cek apakah PHP-FPM sudah aktif:

   ```bash
   sudo systemctl status php8.3-fpm
    ```
   
4. Konfigurasi Nginx agar bisa membaca PHP
   Nginx tidak secara otomatis tahu cara memproses file PHP. Anda harus memberitahu Nginx untuk mengirim file .php ke PHP-FPM.
   Buka file konfigurasi Nginx (misalnya di /etc/nginx/sites-available/default):

   ```bash
   sudo nano /etc/nginx/sites-available/default
    ```

   Cari bagian location ~ \.php$, lalu ubah atau pastikan isinya seperti ini:

   ```bash
   location ~ \.php$ {
      include snippets/fastcgi-php.conf;
       
      # Pastikan socket mengarah ke php8.3-fpm yang baru diinstal
      fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
   }
   
   # Tambahkan index.php agar diutamakan saat membuka web
   index index.php index.html index.htm;
    ```
   
5. Test dan Restart Nginx

   ```bash
   sudo nginx -t
   sudo systemctl restart nginx
    ```
   
6. Uji Coba
   Buat file info untuk memastikan PHP sudah berjalan di browser:

   ```bash
   echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
    ```
   
   Buka browser dan akses: http://localhost/info.php
   
