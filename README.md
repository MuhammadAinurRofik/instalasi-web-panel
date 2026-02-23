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

### 4. install MYSQL
bisa menggunakan tutorial di bawah ini ataupun tutorial dari video lainnya

```bash
https://www.youtube.com/watch?v=9z0pxkpWCJE&t=472s
```

### 5. install phpmyadmin di nginx
bisa menggunakan tutorial di bawah ini ataupun tutorial dari video lainnya

```bash
https://www.youtube.com/watch?v=vqvRWI15q9A&t=388s
```

### 📢 jika ada error mengenai apache pada saat install phpmyadmin 
Error ini terjadi karena phpMyAdmin mencoba menginstal Apache secara paksa sebagai dependensinya, dan Apache mencoba berjalan di Port 80 yang saat ini sudah dikuasai oleh Nginx.
1. Hentikan dan Hapus Apache
   Kita harus membuang Apache yang baru saja "menyelinap" masuk saat instalasi phpMyAdmin.
   
   ```bash
   sudo systemctl stop apache2
   sudo apt purge apache2* -y
   sudo apt autoremove -y
   ```

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
   - Akses via Browser: Buka browser Anda (Chrome, Firefox, dll) dan ketikkan alamat IP server Anda diikuti dengan /phpmyadmin. Contoh: http://192.168.43.30/phpmyadmin/
   - Buat User Admin Baru untuk phpMyAdmin
   - Masuk ke MySQL terminal:

     ```bash
     sudo mysql
     ```

   - Buat user baru (Contoh nama: admin):
     Ganti 'PasswordKuat123!' dengan password yang diinginkan.
     
     ```bash
     CREATE USER 'admin'@'localhost' IDENTIFIED BY 'PasswordKuat123!';
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

   - Masukkan Kredensial di Browser
     Username: admin
     Password: PasswordKuat123! (sesuai password yang di buat)

### 6. install python
- Tambahkan Repository Python

  ```bash
  sudo add-apt-repository ppa:deadsnakes/ppa -y
  sudo apt update
  ```

- Instal Python 3.10 dan Kelengkapannya

  ```bash
  sudo apt update && sudo apt install -y python3.10 python3.10-dev python3.10-venv python3-pip python3.10-distutils libmysqlclient-dev pkg-config
  ```

### 7. install composer
- Download Installer Composer
  Unduh skrip instalasi resminya ke folder /tmp:

```bash
curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php
```

- Verifikasi dan Instalasi Global
  Memproses instalasi dan memindahkan file eksekusinya ke /usr/local/bin/ agar bisa diakses dari folder mana saja.

```bash
sudo php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer
```

- Verifikasi Instalasi
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

### 11. Konfigurasi nginx.conf (Bucket Size)
Menambah alokasi memori Nginx untuk menampung banyak subdomain user.
Edit: sudo nano /etc/nginx/nginx.conf
Cari di dalam blok http { ... } dan tambahkan/ubah:
- client_max_body_size 1024M;
- server_names_hash_bucket_size 128;

### 12. Pengaturan Izin Folder (Permissions)
Memberikan hak akses kepada sistem web untuk mengelola folder /var/www

```bash
sudo chown -R www-data:www-data /var/www
```

```bash
sudo chmod -R 775 /var/www
```





























   
