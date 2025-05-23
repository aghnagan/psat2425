# Penilaian Sumatif Akhir Tahun
## Mapil DevOps XI TJKT 1 - Penilaian Praktek
### SMKN 1 Banyumas - TA. 2024 2025


#
# 🚀 Panduan Deploy Aplikasi Otomatis dengan File .env dan otomatis.sh

🧠 Apa Itu File .env?
File .env adalah file environment yang menyimpan data penting seperti:
> Nama database
> Username database
> Password database
> Host atau alamat server
File ini menyimpan informasi rahasia dan konfigurasi penting yang sebaiknya tidak ditulis langsung di dalam kode program.
#
Contoh isi file .env:
```.env
DB_USER=admin  
DB_PASS=P4ssw0rd123  (disesuaikan dengan keinginan)
DB_NAME=AGHNA1      (nama database yang ada di RDS)  
DB_HOST=rdsku.czt6n8ylfvyb.us-east-1.rds.amazonaws.com (EndpoinT RDS)
```
#

## 1. PERSIAPAN FILE
a. Mengunduh semua file yang ada di https://github.com/paknux/psat2425 
    1. Dengan cara pergi ke halaman link tersebut
    2. Klik menu atau tombol Code, yang berwarna hijau
    3. Pilih Download ZIP, untuk mengunduh kode programnya
    4. Buka folder ~> cari kode program yang sudah di unduh
    5. Copy kode program tersebut ~> lalu pindahkan atau paste ke dalam folder XAMPP ~> htdocs ~> Extract Here
    6. Buka folder kode program di VSCode
    7. Ctrl+S ~> semua filenya (agar filenya tersimpan)
    8. Tambahkan file .env dan biarkan kosong dulu
    9. Pastikan file .gitignore ada (jika belum, buatlah filenya)
        File ini berfungsi untuk mengabaikan file sensitif
        ~> contoh : .env               # Abaikan file .env
                    uploads/*          # Abaikan semua isi folder uploads
                    !uploads/          # Tapi tetap sertakan folder uploads-nya
#
b. Membuat Repository Github
    1. Pergi ke github 
    2. Klik Home
    3. Klik New ~> pada Top Repositories
    4. Isi Repository Name ~> psat2425 (disesuaikan)
    5. Pilih Public, agar dapat dilihat oleh orang lain
    6. Create Repository
        ~> Contoh hasil : https://github.com/aghnagan/psat2425
#  
c. Mengepush ke Github
    1. Buka Terminal ~> New Terminal
    2. Ketik git init
    3. git remote add origin [tambahkan link repository]
        ~> contoh : git remote add origin https://github.com/aghnagan/psat2425
    4. git add .
    5. git commit -m "first commit"
    6. git config --global user.email [email yang digunakan untuk login ke Github]
    7. git config --global user.name [nama repository]
    8. git branch -M main
    9. git push -u origin main
    10 Jika sudah berhasil, isi folder atau file yang dipush akan muncul di Github
#
## 2. MASUK KE AWS
    1. Cari awsacademy.instructure.com
    2. Login
    3. Klik Modules
    4. Pilih Meluncurkan AWS 
    5. Klik Start Lab (tunggu proses loadingnya selesai)
    6. Klik AWS (yang sudah bertanda hijau)
#
## 3. MEMBUAT DATABASE DI AWS (AURORA & RDS)
    1. Cari Aurora and RDS
    2. Klik Create Database
    3.Engine options pilih: MySQL
    4. Template: Free Tier
    5. Beri nama: DB Instance Name (contoh: AGHNA1, disesuaikan saja)
    6. Beri username pada: Master username
    7. Buat password pada: Master password
    8. Public Access: No
    9. Tambahkan security groups: yang mengizinkan port 3306 (MySQL / MariaDB)
    10. Jika sudah klik: Create database
    11. Tunggu hingga "Endpoint" muncul
#
## 4. MEMBUAT INTANCES
    1. Beri nama instance pada bagian: Name
    2. Pilih: OS Ubuntu
    3. Instance type: t2.nano
    4. Key pair: vockey
    5. Security group:
        ~> Allow SSH (port 22)
        ~> Allow HTTPS (port 443)
        ~> Allow HTTP (port 80)
        ~> Anywhere 0.0.0.0/0
#
## 5. Tambahkan Shell Script Otomatis
        Untuk mendeploy otomatis lewat script:
        Saat pembuatan instance:
        ~> Klik: Advanced Details
        ~> Di bagian User Data, masukkan:
            #!/bin/bash
            #!/bin/bash
            sudo apt update -y
            sudo apt install -y apache2 php php-mysql libapache2-mod-php mysql-client
            sudo rm -rf /var/www/html/{,.}
            sudo git clone [link repo kamu] /var/www/html
            sudo chmod -R 777 /var/www/html
            echo DB_USER=[username rds] > /var/www/html/.env
            echo DB_PASS=[password rds] >> /var/www/html/.env
            echo DB_NAME=[nama database] >> /var/www/html/.env
            echo DB_HOST=[endpoint rds] >> /var/www/html/.env
            sudo apt install -y openssl
            sudo a2enmod ssl
            sudo a2ensite default-ssl.conf
            sudo systemctl reload apache2' > /home/ubuntu/otomatis.sh

            chmod +x /home/ubuntu/otomatis.sh
    
    Penjelasan:
    - contoh sudo git clone: sudo git clone https://github.com/aghnagan/psat2425 /var/www/html/.env
    - DB_USER=.... ~> username yang dibuat di RDS (Master Username)
    - DB_PASS=.... ~> password yang dibuat di RDS (Master Password)
    - DB_NAME=.... ~> nama database untuk aplikasi (boleh buat sendiri)
    - DB_HOST=.... ~> endpoint di database "Aurora and RDS"

        ~> Pastikan semuanya sudah terisi dengan benar
        ~> Jika sudah klik "Launch Instance"
        ~> Jika instance tidak muncul refresh halaman instances 
#
### 6. MENJALANKAN SCRIPT
        1. Masuk ke instance: Klik Instance ID → Connect
        2. scroll ke bawah klik: Connect
        2. Gunakan perintah ini, untuk menjalankan script: ./otomatis.sh
#
## 7. MENGECEK AKSES MENGGUNAKAN IP
       ~> Gunakan public IP instance EC2 
#
## 8. MENJALANKAN SCRIPT MANUAL SETELAH INTANCES AKTIF
    1. Membuka terminal instance
    2. klik "Instance ID" ke: instance yang sudah dibuat tadi
    3. Klik: Connect
    4. Scroll ke bawah, klik : Connect
    5. $ nano otomatis.sh
    6. #!/bin/bash
        sudo apt update -y
        sudo apt install -y apache2 php php-mysql libapache2-mod-php mysql-client
        sudo rm -rf /var/www/html/{,.}
        sudo git clone [repository githubmu] /var/www/html
        sudo chmod -R 777 /var/www/html
        echo DB_USER=[username rds] > /var/www/html/.env
        echo DB_PASS=[password rds]  >> /var/www/html/.env
        echo DB_NAME=[nama database]  >> /var/www/html/.env
        echo DB_HOST=[endpoint rds] >> /var/www/html/.env
        sudo apt install -y openssl
        sudo a2enmod ssl
        sudo a2ensite default-ssl.conf
        sudo systemctl reload apache2' > /home/ubuntu/otomatis.sh

        chmod +x /home/ubuntu/otomatis.sh  
    7. Ketik: $ chmod +x otomatis.sh
    8. Jalankan dengan: $ ./otomatis.sh
#
## 📘 PENJELASAN SCRIPT OTOMATIS
        Perintah	                                    Fungsi
    ~> sudo apt update -y	                 =  Memperbarui daftar paket
    ~> sudo apt install ...	                 =  Menginstall Apache, PHP, MySQL client
    ~> sudo rm -rf /var/www/html/{,.}	     =  Menghapus semua isi direktori web
    ~> sudo git clone	                     =  Mengambil kode dari GitHub
    ~> sudo chmod -R 777	                 =  Memberikan akses penuh pada direktori
    ~> sudo a2enmod ssl & a2ensite	         =  Mengaktifkan SSL untuk akses HTTPS
    ~> sudo systemctl reload apache2	     =  Reload konfigurasi Apac
#
## 🧪 UJI COBA
    ~> Coba menggunakan ip public, jika berhasil;
        SELAMAT! 🎉 Aplikasi kamu sudah berhasil di-deploy secara otomatis!akses menggunakan ip public
    ~> Jalankan menggunakan username dan password default:
        Username = admin
        Password = 123

#
# ‼️SELAMAT MENCOBA DAN SEMOGA BERHASIL‼️







