# instalasi-web-panel

## 🛠️ Teknologi yang Digunakan
- **Backend:** Python/PHP/Node.js
- **Framework:** Laravel 12
- **Database:** MySQL
- **Server:** Nginx
- **OS:** Ubuntu 22.04 LTS

## 📢 untuk domain disini sementara menggunakan prodi.ac.id karna belum memiliki domain pasti yang aktif, jadi jika domain yang di inginkan bukan prodi.ac.id, itu bisa diganti dengan nama domain lain.

## 📥 Panduan Instalasi di Server
Ikuti langkah-langkah di bawah ini untuk menyiapkan lingkungan server.

📢 jalan kan semua perintah sebagai root.

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
   sudo apt install -y php8.3-fpm php8.3-mysql php8.3-common php8.3-cli php8.3-zip php8.3-gd php8.3-mbstring php8.3-curl php8.3-xml php8.3-intl
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

7. Install Versi php tambahan untuk fitur multi version

   ```bash
   sudo apt install -y php8.2-fpm php8.2-mysql php8.2-xml php8.2-mbstring php8.2-curl php8.2-zip php8.2-gd php8.2-bcmath php8.2-intl\
                     php8.4-fpm php8.4-mysql php8.4-xml php8.4-mbstring php8.4-curl php8.4-zip php8.4-gd php8.4-bcmath php8.4-intl
   ```

### 4. install MYSQL
bisa menggunakan tutorial di bawah ini ataupun tutorial dari video lainnya

```bash
https://www.youtube.com/watch?v=9z0pxkpWCJE&t=472s menit 2:47 - 4:46
```

Ringkasan videonya:
1. jalankan perintah ini, dan tunggu hingga selesai

   ```bash
   sudo apt install mysql-server -y
   ```

2. kemudian cek apakah mysql sudah aktif atau belum

   ```bash
   # cek status
   sudo systemctl status mysql

   # jika belum aktif jalankan ini
   sudo systemctl start mysql
   sudo systemctl enable mysql
   ```

3. kemudian jalankan perintah ini

   ```bash
   sudo /usr/bin/mysql_secure_installation

   # ikuti tahapan ini sampai selesai
   Securing the MySQL server deployment.

   Connecting to MySQL using a blank password.
   
   VALIDATE PASSWORD COMPONENT can be used to test passwords
   and improve security. It checks the strength of password
   and allows the users to set only those passwords which are
   secure enough. Would you like to setup VALIDATE PASSWORD component?
   
   Press y|Y for Yes, any other key for No: n
   
   Skipping password set for root as authentication with auth_socket is used by default.
   If you would like to use password authentication instead, this can be done with the "ALTER_USER" command.
   See https://dev.mysql.com/doc/refman/8.0/en/alter-user.html#alter-user-password-management for more information.
   
   By default, a MySQL installation has an anonymous user,
   allowing anyone to log into MySQL without having to have
   a user account created for them. This is intended only for
   testing, and to make the installation go a bit smoother.
   You should remove them before moving into a production
   environment.
   
   Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
   Success.
   
   
   Normally, root should only be allowed to connect from
   'localhost'. This ensures that someone cannot guess at
   the root password from the network.
   
   Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
   Success.
   
   By default, MySQL comes with a database named 'test' that
   anyone can access. This is also intended only for testing,
   and should be removed before moving into a production
   environment.
   
   
   Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
    - Dropping test database...
   Success.
   
    - Removing privileges on test database...
   Success.
   
   Reloading the privilege tables will ensure that all changes
   made so far will take effect immediately.
   
   Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
   Success.
   
   All done!
   ```
   
### 5. install phpmyadmin di nginx
bisa menggunakan tutorial di bawah ini ataupun tutorial dari video lainnya

```bash
https://www.youtube.com/watch?v=vqvRWI15q9A&t=388s menit 3:06 
```

Ringkasan Video:
1. jalankan perintah ini untuk install phpmyadmin, dan tunggu hinngga selesai
   
   ```bash
   sudo apt install phpmyadmin -y
   ```

2. jika muncul "Configuring phpmyadmin" untuk memilih web server, jangan pilih apapun, langsung ok saja (Tab + enter), karna disini kita akan memakai nginx, dan di pilihan itu tidak ada nginx.
3. jika muncul "Configuring phpmyadmin"

   ```bash
      The phpmyadmin package must have a database installed and configured   │
    │ before it can be used. This can be optionally handled with             │
    │ dbconfig-common.                                                       │
    │                                                                        │
    │ If you are an advanced database administrator and know that you want   │
    │ to perform this configuration manually, or if your database has        │
    │ already been installed and configured, you should refuse this option.  │
    │ Details on what needs to be done should most likely be provided in     │
    │ /usr/share/doc/phpmyadmin.                                             │
    │                                                                        │
    │ Otherwise, you should probably choose this option.                     │
    │                                                                        │
    │ Configure database for phpmyadmin with dbconfig-common?  
   ```

   pilih yes

4. kemudian muncul "Configuring phpmyadmin" untuk membuat password phpmyadmin dan juga password confirmationnya

Setelah Menginstall Phpmyadmin, seharusnya akan terinstall juga versi php terbaru (seperti php 8.5), jika versi yang ingin di pakai adalah versi 8.3, maka kita harus menghapus versi 8.5 dan mensetting versi 8.3 menjadi default.

1. cek ada berapa versi php didalam mesin/server

   ```bash
   # cek versi php
   php -v
   ```
   
   ```bash
   # untuk melihat versi mana saja yang terdaftar di sistem dan mana yang sedang aktif.
   sudo update-alternatives --display php
   ```
   
   ```bash
   # Untuk melihat semua paket PHP (termasuk modul dan FPM) yang terinstal di sistem.
   dpkg -l | grep php | grep -E "cli|fpm"
   ```
   
   ```bash
   # cek file eksekusinya di folder /usr/bin/
   ls /usr/bin/php*
   ```

   jika yang muncul adalah bukan versi yang ingin dipakai (versi 8.3), atau ada lebih dari 1 versi, maka hapus versi yang tidak ingin dipakai dan setting versi yang ingin dipakai menjadi default.

2. Paksa Default PHP ke Versi 8.3
   Gunakan fitur update-alternatives milik Ubuntu untuk memilih versi mana yang menjadi prioritas utama di terminal.

   ```bash
   sudo update-alternatives --set php /usr/bin/php8.3
   sudo update-alternatives --set phar /usr/bin/phar8.3
   sudo update-alternatives --set phar.phar /usr/bin/phar.phar8.3
   ```

   Setelah menjalankan perintah di atas, coba cek lagi:

   ```bash
   php -v (Seharusnya sekarang muncul 8.3).
   ```

3. Hapus PHP 8.5 (atau versi lain yang ingin dihapus)
   
   ```bash
   sudo apt purge php8.5* -y
   sudo apt autoremove -y
   ```

   Setelah berhasil menghapus, cek kembali apakah masih ada versi php selain 8.3, jika ada, maka hapus jika memang tidak diperlukan.

### 📢 jika ada error mengenai apache pada saat install phpmyadmin 
Error ini terjadi karena phpMyAdmin mencoba menginstal Apache secara paksa sebagai dependensinya, dan Apache mencoba berjalan di Port 80 yang saat ini sudah dikuasai oleh Nginx.
1. Hentikan dan Hapus Apache
   Kita harus membuang Apache yang baru saja "menyelinap" masuk saat instalasi phpMyAdmin.
   
   ```bash
   sudo systemctl stop apache2
   sudo apt purge apache2* -y
   sudo apt autoremove -y
   ```

   📢 jika tidak ada error, maka lanjutkan ke tahap menambahkan blok khusus phpmyadmin

2. Tambahkan Blok Khusus phpMyAdmin
   Buka konfigurasi Nginx:

   ```bash
   sudo nano /etc/nginx/sites-available/default
   ```

   Di dalam blok server { ... }, tambahkan konfigurasi berikut tepat di atas blok location /:

   ```bash
   location /phpmyadmin {
       root /usr/share/;
       index index.php index.html index.htm;
   
       location ~ ^/phpmyadmin/(.+\.php)$ {
           try_files $uri =404;
           root /usr/share/;
           fastcgi_pass unix:/var/run/php/php8.4-fpm.sock; # Sesuaikan jika pakai php8.3
           fastcgi_index index.php;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }
   
       location ~* ^/phpmyadmin/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
           root /usr/share/;
       }
   }
   ```

   full nya seperti ini
   
   ```bash
   ##
   # You should look at the following URL's in order to grasp a solid understanding
   # of Nginx configuration files in order to fully unleash the power of Nginx.
   # https://www.nginx.com/resources/wiki/start/
   # https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/
   # https://wiki.debian.org/Nginx/DirectoryStructure
   #
   # In most cases, administrators will remove this file from sites-enabled/ and
   # leave it as reference inside of sites-available where it will continue to be
   # updated by the nginx packaging team.
   #
   # This file will automatically load configuration files provided by other
   # applications, such as Drupal or Wordpress. These applications will be made
   # available underneath a path with that package name, such as /drupal8.
   #
   # Please see /usr/share/doc/nginx-doc/examples/ for more detailed examples.
   ##
   
   # Default server configuration
   #
   server {
   	      listen 80 default_server;
   	      listen [::]:80 default_server;
   
         	# SSL configuration
         	#
         	# listen 443 ssl default_server;
         	# listen [::]:443 ssl default_server;
         	#
         	# Note: You should disable gzip for SSL traffic.
         	# See: https://bugs.debian.org/773332
         	#
         	# Read up on ssl_ciphers to ensure a secure configuration.
         	# See: https://bugs.debian.org/765782
         	#
         	# Self signed certs generated by the ssl-cert package
         	# Don't use them in a production server!
         	#
         	# include snippets/snakeoil.conf;
   
   	      root /var/www/html;
   
         	# Add index.php to the list if you are using PHP
         	index index.html index.htm index.nginx-debian.html;
         
         	server_name _;
   
         	location /phpmyadmin {
             	root /usr/share/;
               index index.php index.html index.htm;
         
            	location ~ ^/phpmyadmin/(.+\.php)$ {
                  try_files $uri =404;
                  root /usr/share/;
                  fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
                  fastcgi_index index.php;
                  fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                  include fastcgi_params;
             	}
         
             	location ~* ^/phpmyadmin/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
                  root /usr/share/;
             	}
            }
         
         	location / {
         		# First attempt to serve request as file, then
         		# as directory, then fall back to displaying a 404.
         		try_files $uri $uri/ =404;
         	}
            
         	# pass PHP scripts to FastCGI server
         	#
         	location ~ \.php$ {
         		      include snippets/fastcgi-php.conf;
         	#
         	#	# With php-fpm (or other unix sockets):
         		fastcgi_pass unix:/run/php/php8.3-fpm.sock;
         	#	# With php-cgi (or other tcp sockets):
         	#	fastcgi_pass 127.0.0.1:9000;
         	}
         
         	# deny access to .htaccess files, if Apache's document root
         	# concurs with nginx's one
         	#
         	#location ~ /\.ht {
         	#	deny all;
         	#}
   }
   
   # Virtual Host configuration for example.com
   #
   # You can move that to a different file under sites-available/ and symlink that
   # to sites-enabled/ to enable it.
   #
   #server {
   #	     listen 80;
   #	     listen [::]:80;
   #
   #	     server_name example.com;
   #
   #	     root /var/www/example.com;
   #	     index index.html;
   #
   #	     location / {
   #		          try_files $uri $uri/ =404;
   #	     }
   #}
   ```

3. Restart Nginx:

   ```bash
   sudo systemctl restart nginx
   ```

   Sekarang coba akses melalui browser di http://IP_ADDRESS/phpmyadmin/.
   
   📢 Kenapa Cara Ini Lebih Baik?
   - Tidak Bergantung pada Symlink: Nginx langsung mencari file di /usr/share/phpmyadmin.
   - Lebih Aman: Mengisolasi pengaturan phpMyAdmin dari folder web utama Anda (/var/www/html).
   - Mencegah Error 403: Karena kita mendefinisikan index index.php secara spesifik di dalam lokasi tersebut.

4. Cara masuk ke phpmyadmin
   Setelah konfigurasi Nginx selesai, Anda bisa masuk ke phpMyAdmin melalui browser. Namun, ada satu hal teknis yang perlu Anda ketahui: MySQL 8.0 ke atas tidak mengizinkan user root login ke phpMyAdmin secara default.
   - Buat User Admin Baru untuk phpMyAdmin
   - Masuk ke MySQL terminal:

     ```bash
     sudo mysql
     ```

   - Buat user baru (Contoh nama: admin):
     Ganti 'PasswordKuat123!' dengan password yang diinginkan.
     
     ```bash
     CREATE USER 'admin'@'localhost' IDENTIFIED BY 'Admin123';
     ```

   - Berikan hak akses penuh:
     
     ```bash
     GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost' WITH GRANT OPTION;
     ```

   - Refresh hak akses dan keluar:
  
     ```bash
     FLUSH PRIVILEGES;
     EXIT;
     ```

   - Akses via Browser: Buka browser Anda (Chrome, Firefox, dll) dan ketikkan alamat IP server Anda diikuti dengan /phpmyadmin. Contoh: http://192.168.43.30/phpmyadmin/
   - Masukkan Kredensial di Browser
     Username: admin
     Password: Admin123 (sesuai password yang di buat)

### 6. install python
1. Tambahkan Repository Python

   ```bash
   sudo add-apt-repository ppa:deadsnakes/ppa -y
   sudo apt update
   ```

2. Instal Python 3.10 dan Kelengkapannya

   ```bash
   sudo apt update && sudo apt install -y python3.10 python3.10-dev python3.10-venv python3-pip python3.10-distutils libmysqlclient-dev pkg-config
   ```

3. Install Versi python tambahan untuk fitur multi version

   ```bash
   sudo apt install -y \
   python3.11 python3.11-dev python3.11-venv \
   python3.12 python3.12-dev python3.12-venv python3.12-full
   ```

### 7. install composer
1. Download Installer Composer
   Unduh skrip instalasi resminya ke folder /tmp:

   ```bash
   curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php
   ```

2. Verifikasi dan Instalasi Global
   Memproses instalasi dan memindahkan file eksekusinya ke /usr/local/bin/ agar bisa diakses dari folder mana saja.

   ```bash
   sudo php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer
   ```

3. Verifikasi Instalasi
   Untuk memastikan Composer sudah terpasang dengan benar, jalankan perintah berikut:

   ```bash
   composer --version
   ```

   Pastikan versi php itu konsisten, karena saat menginstal Composer, sistem mendeteksi adanya paket php (metapackage) yang lebih baru di repositori PPA Ondřej Surý yang di tambahkan sebelumnya. Karena PPA tersebut sangat up-to-date, versi PHP 8.5 (yang kemungkinan masih versi development atau baru rilis) otomatis ikut terambil sebagai dependensi terbaru. jika menggunakan php versi 8.3, maka hapus php versi terbarunya. 

### 8. install unzip

```bash
sudo apt install unzip zip curl -y
```

### 9. Konfigurasi Sudoers
Agar PHP (www-data) bisa membuat folder di /var/www dan merestart Nginx via script.
jalankan perintah:

```bash
sudo visudo
```

Tambahkan di baris paling bawah:

```bash
www-data ALL=(ALL) NOPASSWD: /usr/bin/mkdir, /usr/bin/cp, /usr/bin/unzip, /usr/bin/rm, /usr/bin/chown, /usr/bin/chmod, /usr/bin/mv, /usr/bin/ln, /usr/bin/systemctl, /usr/bin/php, /usr/bin/tail -n [0-9]* /var/log/nginx/*.log, /usr/bin/rm -f, /usr/bin/rm -rf /var/log/nginx/*, /usr/bin/rm -f /var/log/nginx/*, /usr/bin/python3.10, /usr/bin/pip, /usr/bin/touch, /usr/bin/tail, /usr/bin/bash, /usr/bin/systemctl reload nginx, /usr/bin/systemctl daemon-reload, /usr/bin/systemctl enable flask_*, /usr/bin/systemctl restart flask_*, /var/www/*/venv/bin/pip cache purge
```

### 10. Konfigurasi php.ini (Upload & Memory)
Ubah batas upload agar file ZIP tidak ditolak oleh server.
Edit: sudo nano /etc/php/8.x/fpm/php.ini (Sesuaikan 8.x dengan versi yang terinstall)
Ubah:
- upload_max_filesize = 1024M
- post_max_size = 1024M
- memory_limit = 1280M
- max_execution_time = 3600

```bash
systemctl restart php8.3-fpm
```

### 11. Konfigurasi nginx.conf (Bucket Size)
Menambah alokasi memori Nginx untuk menampung banyak subdomain user.
Edit: sudo nano /etc/nginx/nginx.conf
Cari di dalam blok http { ... } dan tambahkan/ubah:
- client_max_body_size 1024M;
- server_names_hash_bucket_size 128;

```bash
systemctl restart nginx
```

### 12. Pengaturan Izin Folder (Permissions)
Memberikan hak akses kepada sistem web untuk mengelola folder /var/www

```bash
sudo chown -R www-data:www-data /var/www
```

```bash
sudo chmod -R 775 /var/www
```

### 13. Persiapan Folder & Clone Repository
Masuk ke direktori /var/www dan mengambil kode dari GitHub menggunakan git clone.

```bash
# Masuk ke folder web
cd /var/www

# Clone repository (Ganti URL dengan link repo Anda)
sudo git clone https://github.com/MuhammadAinurRofik/web-panel

# Berikan izin kepemilikan awal agar bisa diproses Laravel
sudo chown -R www-data:www-data /var/www/web-panel
```

### 14. Setup Lingkungan Laravel (.env)
Masuk ke direktori /var/www dan mengambil kode dari GitHub menggunakan git clone.

```bash
cd /var/www/web-panel

# Salin file .env
sudo cp .env.example .env

# Edit file .env (Atur DB_DATABASE, DB_USERNAME, DB_PASSWORD sesuai server)
sudo nano .env

# Install dependencies via Composer
sudo -u www-data composer install --no-dev

# Mengunduh script instalasi NodeSource
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -

# Instalasi Node.js dan NPM sekaligus
sudo apt install -y nodejs

# Verifikasi Instalasi
node -v
npm -v

# Masuk ke folder proyek
cd /var/www/web-panel

# Install semua library yang ada di package.json
sudo -u www-data npm install

# Compile aset untuk produksi (Vite/Mix)
sudo -u www-data npm run build

# Generate key & Migration
php artisan key:generate
php artisan migrate --seed
php artisan storage:link
```

### 15. Konfigurasi Nginx (prodi.ac.id) sesuaikan nama domainnya
Buat file konfigurasi server agar domain/IP bisa mengarah ke folder Laravel tersebut.

1. Buat file konfigurasi baru

   ```bash
   sudo nano /etc/nginx/sites-available/prodi.ac.id
   ```

2. Tempelkan kode berikut (Sudah disesuaikan dengan timeout 1 jam)

   ```bash
   server {
       listen 80;
       server_name prodi.ac.id www.prodi.ac.id;
       root /var/www/web-panel/public;
   
       index index.php index.html;
   
       # Ukuran upload maksimal (sesuaikan dengan php.ini)
       client_max_body_size 128M;
   
       location / {
           try_files $uri $uri/ /index.php?$query_string;
       }
   
       location ~ \.php$ {
           include snippets/fastcgi-php.conf;
           # Pastikan versi socket PHP-FPM benar (cek dengan: ls /var/run/php/)
           fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
   
           # Timeout 1 jam (Sangat penting untuk proses unzip/deployment besar)
           fastcgi_read_timeout 3600;
           fastcgi_connect_timeout 3600;
           fastcgi_send_timeout 3600;
   
           # Pengaturan buffer tambahan
           fastcgi_buffers 16 16k;
           fastcgi_buffer_size 32k;
       }
   
       location ~ /\.ht {
           deny all;
       }
   }
   ```

3. Aktifkan Konfigurasi

   ```bash
   # Link ke folder sites-enabled
   sudo ln -s /etc/nginx/sites-available/prodi.ac.id /etc/nginx/sites-enabled/
   
   # Tes apakah ada pengetikan yang salah
   sudo nginx -t
   
   # Restart Nginx jika OK
   sudo systemctl restart nginx
   ```

### 16. Perbaikan Izin Folder Akhir
Agar Laravel tidak error "Permission Denied" saat menulis log atau mengunggah file zip.

```bash
sudo chown -R www-data:www-data /var/www/web-panel
sudo chmod -R 775 /var/www/web-panel/storage
sudo chmod -R 775 /var/www/web-panel/bootstrap/cache
```

### 17. Cara mengakses jika belum memiliki domain aktif
Agar bisa membuka http://prodi.ac.id (dan bukan IP/prodi.ac.id), kita harus "menipu" laptop agar tahu bahwa domain tersebut ada di IP servermu.

Jika Laptop menggunakan Windows:
1. buka cmd/terminal dan jalankan sebagai administrator (Run as Administrator).
2. jalankan perintah:
   
   ```bash
   notepad C:\Windows\System32\drivers\etc\hosts
   ```
   
3. Tambahkan baris ini di bagian paling bawah:
   
   ```bash
   IP_SERVER    prodi.ac.id
   ```

4. Simpan. Sekarang buka browser dan ketik http://prodi.ac.id.

Jika Laptop menggunakan Ubuntu:
1. Buka terminal di laptop:
   
   ```bash
   sudo nano /etc/hosts
   ```
   
2. Tambahkan baris: 

   ```bash
   IP_SERVER    prodi.ac.id
   ```

   Simpan (Ctrl+O, Enter, Ctrl+X). Kemudian buka browser dan ketik http://prodi.ac.id.







   
