# 📦 T-Backup - Telegram Backup Storage

[![Status](https://img.shields.io/badge/Status-Stable-green?style=for-the-badge)](https://github.com/Mindsetmot/Telegram-Backup-Storage)
[![Language](https://img.shields.io/badge/Language-Bash%20%26%20Python-blue?style=for-the-badge)](https://www.python.org/)
[![Platform](https://img.shields.io/badge/Platform-Termux-orange?style=for-the-badge)](https://termux.dev/)

**T-Backup** adalah tool berbasis Command Line Interface (CLI) untuk lingkungan **Termux** yang berfungsi mengotomatisasi pencadangan data (file & folder) dengan memanfaatkan Channel Telegram sebagai cloud storage tanpa batas. Tool ini dilengkapi dengan **Bot Indexer & Retriever Daemon** yang berjalan di latar belakang untuk memudahkan pencarian dan pengunduhan kembali berkas cadangan langsung lewat chat Telegram.

---

### 🚀 Fitur Utama

* **Direct File Upload**: Mengunggah file tunggal berukuran besar langsung ke cloud Telegram dengan ringkas.
* **Smart Folder Backup**: Mengompresi folder menjadi arsip `.tar.gz` secara otomatis sebelum diunggah.
* **Background Bot Indexer**: Menyediakan bot pencari (`tbackup_bot.py`) yang berjalan sunyi di background menggunakan `nohup`.
* **Auto Dependency Installer**: Mendeteksi dan memasang otomatis paket Python 3, `jq`, dan library `Telethon` saat pertama kali dijalankan.
* **Secure Configurations**: Menyimpan kredensial dengan aman di file konfigurasi lokal `~/.tbackup.conf`.

---

### 🛠️ Langkah Instalasi (Tanpa Git Clone)

Buka aplikasi Termux kamu, lalu jalankan perintah-perintah berikut berturut-turut:

#### 1. Update & Upgrade Repositori Termux
```bash
pkg update -y && pkg upgrade -y

```

#### 2. Install Dependency Awal (Curl & Python)

```bash
pkg install curl python -y

```

#### 3. Unduh Script Utama T-Backup

Perintah ini akan mengunduh script utama dan menempatkannya langsung ke direktori eksekusi global agar bisa dipanggil dari mana saja:

```bash
curl -L -o "$PREFIX/bin/T-Backup" "https://raw.githubusercontent.com/Mindsetmot/Telegram-Backup-Storage/main/T-Backup.sh"

```

#### 4. Beri Izin Eksekusi pada Script Utama

```bash
chmod +x "$PREFIX/bin/T-Backup"

```

#### 5. Unduh Script Bot Pencari (Kunci / Obfuscated)

Unduh komponen pendukung bot dan letakkan di folder Home (`~`):

```bash
curl -L -o "$HOME/tbackup_bot.py" "https://raw.githubusercontent.com/Mindsetmot/Telegram-Backup-Storage/main/tbackup_bot.py"

```

---

### 🔑 Panduan Mendapatkan Kredensial Telegram

Saat pertama kali menjalankan `T-Backup`, kamu akan diminta memasukkan 5 konfigurasi penting. Berikut cara mendapatkannya:

#### 1. Mendapatkan `API_ID` dan `API_HASH`

Kedua ID ini diperlukan agar script Python kamu bisa terhubung ke jaringan core Telegram API (*Userbot basis*).

1. Buka browser dan masuk ke situs resmi: [https://my.telegram.org](https://my.telegram.org).
2. Masukkan nomor telepon akun Telegram kamu (gunakan format internasional, misal: `+628xxxx`).
3. Masukkan kode verifikasi yang dikirimkan langsung ke aplikasi Telegram kamu.
4. Pilih menu **API development tools**.
5. Isi kolom **App title** dan **Short name** secara bebas (misal: `TermuxBackupTool`). Kolom URL kosongi saja.
6. Klik **Create application**.
7. Kamu akan melihat **App api_id** (berupa angka) dan **App api_hash** (berupa kombinasi huruf & angka). Simpan kedua data tersebut.

#### 2. Mendapatkan `BOT_TOKEN`

Token ini digunakan agar bot pencari dapat merespons perintah `/cari` kamu.

1. Buka aplikasi Telegram, cari bot resmi **[@BotFather](https://t.me/BotFather)**.
2. Kirim pesan `/newbot`.
3. Masukkan nama bebas untuk bot kamu (misal: `My TBackup Storage`).
4. Masukkan *username* unik untuk bot kamu yang diakhiri kata `bot` (misal: `mindsetmot_backup_bot`).
5. `@BotFather` akan mengirimkan pesan balasan berisi kode **HTTP API Token** (contoh: `123456789:ABCdefGhIJKlmNoPQRsT...`). Copy token tersebut.

#### 3. Mendapatkan `USER_ID` Kamu

ID ini berfungsi sebagai pengunci keamanan (*whitelist admin*) agar hanya kamu yang bisa memerintah bot tersebut.

1. Cari bot **[@userinfobot](https://t.me/userinfobot)** di Telegram.
2. Kirim pesan apa saja atau klik `/start`.
3. Bot akan membalas dengan menampilkan informasi profilmu. Salin deretan angka yang tertera pada baris **Id** (contoh: `598765432`).

#### 4. Mendapatkan `CHANNEL_ID` Penyimpanan

Channel ini digunakan sebagai wadah "gudang" tempat seluruh file backup kamu dilempar dan disimpan.

1. Buat sebuah **Channel Baru** di Telegram (Bisa disetel sebagai *Private* demi keamanan data pribadi).
2. **Wajib:** Masukkan bot yang tadi kamu buat di `@BotFather` ke dalam channel tersebut, lalu **jadikan bot sebagai Admin** dengan izin minimal *Post Messages* (Mengirim Pesan).
3. Untuk mengetahui ID Channel tersebut, buat satu postingan/pesan acak di dalam channel tersebut, lalu **Forward (Teruskan)** pesan tersebut ke bot **[@ShowJsonBot](https://www.google.com/search?q=https://t.me/ShowJsonBot)** atau **[@MissRose_bot](https://t.me/MissRose_bot)**.
4. Cari bagian ID Channel pada balasan bot. ID Channel Telegram selalu diawali dengan angka `-100` (contoh: `-1002134567890`). Salin kode lengkap beserta tanda minusnya.

---

### 🚀 Cara Menjalankan Tool

Setelah instalasi dan persiapan kredensial di atas selesai, kamu cukup mengetik perintah ini di sesi Termux kamu:

```bash
T-Backup

```

* **Jalankan Pertama Kali:** Sistem akan otomatis mendeteksi bahwa kamu belum mengonfigurasinya dan langsung membuka **Wizard Setup**. Masukkan ke-5 item yang sudah kamu siapkan tadi satu per satu.
* **Menyalakan Bot:** Setelah masuk ke Main Menu, pilih menu **`[03] Start/Stop Bot Indexer`** untuk menghidupkan bot pencari di background. Sekarang kamu bisa mencari file backup-an kamu kapan saja langsung melalui chat Telegram Bot pribadi kamu!

---
