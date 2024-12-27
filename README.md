#!/bin/bash

# Script untuk menginstal tema khusus Pterodactyl

echo "======================================="
echo "  Instalasi Tema Pterodactyl Otomatis"
echo "======================================="

# 1. Unduh file tema
echo "Mengunduh file tema..."
wget -O /tmp/Pterodactyl_Custom_Theme.zip "https://sandbox:/mnt/data/Pterodactyl_Custom_Theme.zip"
if [ $? -ne 0 ]; then
    echo "Gagal mengunduh file tema. Periksa tautannya."
    exit 1
fi

# 2. Ekstrak file ZIP
echo "Mengekstrak file tema..."
unzip -o /tmp/Pterodactyl_Custom_Theme.zip -d /tmp/Pterodactyl_Custom_Theme
if [ $? -ne 0 ]; then
    echo "Gagal mengekstrak file tema."
    exit 1
fi

# 3. Pindahkan file tema ke direktori Pterodactyl
echo "Memindahkan file tema ke direktori Pterodactyl..."
cp -r /tmp/Pterodactyl_Custom_Theme/resources/* /var/www/pterodactyl/resources/
if [ $? -ne 0 ]; then
    echo "Gagal memindahkan file tema."
    exit 1
fi

# 4. Pastikan izin file yang benar
echo "Mengatur izin file..."
chown -R www-data:www-data /var/www/pterodactyl/resources
chmod -R 755 /var/www/pterodactyl/resources

# 5. Instal dependensi dan build ulang aset
echo "Menginstal dependensi dan membangun ulang aset..."
cd /var/www/pterodactyl
npm install && npm run build
if [ $? -ne 0 ]; then
    echo "Gagal membangun ulang aset. Periksa konfigurasi Node.js/NPM Anda."
    exit 1
fi

# 6. Restart panel
echo "Merestart panel Pterodactyl..."
systemctl restart pteroq
if [ $? -ne 0 ]; then
    echo "Gagal merestart panel Pterodactyl."
    exit 1
fi

echo "======================================="
echo "  Instalasi Tema Berhasil!"
echo "======================================="
exit 0
