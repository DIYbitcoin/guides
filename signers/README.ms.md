# 📟 Signer DIY: SeedSigner, Kern dan KISS Signer

Signer ialah peranti kecil luar talian yang menyimpan kunci bitcoin anda dan
menandatangani transaksi, jadi kunci anda tidak pernah menyentuh komputer
atau telefon. Panduan ini memasang ("flash") signer sumber terbuka pada
[Waveshare ESP32-P4-WIFI6-Touch-LCD-3.5](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-3.5.htm),
papan skrin sentuh 3.5 inci dengan kamera dan slot microSD. Tidak perlu memateri.

📷 **Cara menggunakannya:** signer tidak pernah bersambung ke internet. Anda
menggandingkannya dengan aplikasi dompet seperti Sparrow dengan mengimbas
kod QR menggunakan kameranya, dan menandatangani transaksi dengan cara yang sama.

⚠️ **Ketiga-tiganya ialah versi awal pada papan ini. Gunakan perkataan benih
latihan dan testnet (bitcoin latihan yang tiada nilai) atau jumlah yang sangat
kecil sahaja, jangan sekali-kali gunakan simpanan sebenar anda.**

### 🧰 Apa yang anda perlukan

- 📟 Papan Waveshare 3.5 inci
- 🔌 Kabel USB-C yang boleh membawa data. Kabel cas sahaja menghidupkan
  papan, tetapi komputer tidak nampak apa-apa.
- 💻 Komputer: Mac atau Linux untuk SeedSigner; mana-mana komputer dengan
  **Chrome** atau **Brave** untuk Kern dan KISS Signer

### 👉 Pilih signer anda

Setiap satu ialah laluan lengkap, dari menyambungkan papan hingga skrin
pertama signer. Ikut yang anda pilih sahaja.

- 🌐 **[Kern](#kern):** paling mudah. Flash dalam Chrome atau Brave dengan
  beberapa klik.
- 🌐 **[KISS Signer](#kiss):** juga di-flash dalam Chrome atau Brave.
- 💻 **[SeedSigner](#seedsigner):** di-flash dengan menaip beberapa arahan
  dalam Terminal. Empat halaman pendek.

<a id="kern"></a>

## 🌐 Kern

Anda perlukan **Chrome** atau **Brave**. Safari dan Firefox tidak boleh
flash.

1. Pasang kabel USB-C ke port pada papan yang bertanda **UART**, dan
   hujung satu lagi ke komputer anda.

   🐧 **Di Linux**, buat [langkah Linux](#linux-kern) di bawah dahulu.

2. Buka [pemancar web Kern](https://odudex.github.io/Kern/flash/).
3. Biarkan **📦 Latest CI Build** dipilih.
4. Di bawah **Board**, pilih **Waveshare ESP32-P4 3.5" (320×480 SPI)**.
   Senarai bermula pada papan lain, jadi semak yang ini.
5. Klik **🔌 Connect**. Tetingkap kecil terbuka: pilih papan anda di situ
   (🍎 namanya ada `usbmodem`, 🐧 `ttyACM`), kemudian klik **Sambung**.

   ✅ Kotak hitam di bawah butang memaparkan `Connected to: ESP32-P4`.
   Pemancar membaca cip anda dan memilih binaan v1.x atau v3.x sendiri.

6. Klik **⚡ Flash** dan tunggu, tanpa mencabut kabel.

   ✅ Ia memaparkan `Device reset. Flash successful! ✓`.

7. Cabut papan, tunggu 3 saat, pasang semula.

   ✅ Kern bermula pada papan. 🎉

<details id="linux-kern">
<summary><b>🐧 Langkah Linux: benarkan pelayar bercakap dengan papan</b></summary>

1. Buka **Terminal**: tekan **Ctrl + Alt + T**.
2. Jalankan:

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 Ia meminta kata laluan komputer anda. Tiada apa-apa dipaparkan
   semasa anda menaip; itu normal, taip sahaja dan tekan **Enter**.

3. **Log keluar dari komputer anda dan log masuk semula**, kemudian
   kembali ke langkah 2 di atas.

</details>

📦 [Keluaran rasmi Kern](https://github.com/odudex/Kern/releases) hanya
untuk cip **v1.x**, jadi gunakan pemancar web.

Seterusnya: [🔋 pasang bateri](#battery), atau
[🩺 penyelesaian masalah](#troubleshooting) jika ada yang tidak kena.

<a id="kiss"></a>

## 🌐 KISS Signer

Anda perlukan **Chrome** atau **Brave**. Safari dan Firefox tidak boleh
flash.

1. Pasang kabel USB-C ke port pada papan yang bertanda **UART**, dan
   hujung satu lagi ke komputer anda.

   🐧 **Di Linux**, buat [langkah Linux](#linux-kiss) di bawah dahulu.

2. Buka [halaman pemasangan KISS Signer](https://diybitcoin.github.io/kiss-signer/).
3. Di bawah **Which board is it?**, pilih **Waveshare 3.5in**.
4. Tandakan kotak **This erases the device. I'm starting with keys that
   hold nothing.**
5. Klik **Connect and install**. Tetingkap kecil terbuka: pilih papan anda
   di situ (🍎 namanya ada `usbmodem`, 🐧 `ttyACM`), kemudian klik
   **Sambung**.
6. Dalam tetingkap seterusnya, klik **Install KISS Signer (Waveshare
   3.5in)**. Tandakan **Erase device**, klik **Next**, kemudian klik
   **Install**. Tunggu, tanpa mencabut kabel.

   ✅ Ia memaparkan **Installation complete!**

7. Cabut papan, tunggu 3 saat, pasang semula.

   ✅ Papan bermula pada **FRUIT ISLAND**, permainan yang KISS Signer
   tunjukkan dahulu. 🎉

8. Skrin kekal hitam? Papan anda ada cip **v3.x** yang lebih baharu, dan
   keluaran ini untuk v1.x. Flash semula dari
   [pemasang ujian](https://diybitcoin.github.io/kiss-signer/test/):
   pilih papan anda di bawah **1. Your board**, kemudian klik **Connect
   and install**. Ia membaca cip anda dan memilih binaan yang sesuai.

   ⚠️ Pemasang ujian ada versi ujian yang tidak ditandatangani, belum
   dicuba pada papan v3.x sebenar.

<details id="linux-kiss">
<summary><b>🐧 Langkah Linux: benarkan pelayar bercakap dengan papan</b></summary>

1. Buka **Terminal**: tekan **Ctrl + Alt + T**.
2. Jalankan:

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 Ia meminta kata laluan komputer anda. Tiada apa-apa dipaparkan
   semasa anda menaip; itu normal, taip sahaja dan tekan **Enter**.

3. **Log keluar dari komputer anda dan log masuk semula**, kemudian
   kembali ke langkah 2 di atas.

</details>

Seterusnya: [🔋 pasang bateri](#battery), atau
[🩺 penyelesaian masalah](#troubleshooting) jika ada yang tidak kena.

<a id="seedsigner"></a>

## 💻 SeedSigner 1: sediakan komputer anda

SeedSigner untuk papan ini ialah versi awal oleh Keith (kdmukai). Anda
flash dengan menaip arahan dalam **Terminal** menggunakan `esptool`, alat
daripada pembuat cip. Setiap arahan di bawah ada butang **salin**: salin,
tampal ke dalam Terminal, tekan **Enter**.

<details>
<summary><b>🍎 Saya guna Mac</b></summary>

1. Buka **Terminal**: tekan **Cmd + Space**, taip `Terminal`, tekan
   **Enter**.

2. Pasang **Homebrew**, pemasang aplikasi Mac untuk Terminal. Langkau
   jika anda sudah ada (`brew --version` memaparkan versi).

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 Ia meminta kata laluan Mac anda. Tiada apa-apa dipaparkan semasa
   anda menaip; itu normal, taip sahaja dan tekan **Enter**.

   ⚠️ Apabila selesai, ia memaparkan **Next steps** dengan dua atau tiga
   arahan. Salin dan jalankan juga, atau Mac tidak akan jumpa `brew`.

3. Pasang esptool:

   ```bash
   brew install esptool
   ```

</details>

<details>
<summary><b>🐧 Saya guna Linux</b></summary>

1. Buka **Terminal**: tekan **Ctrl + Alt + T**.

2. Benarkan pengguna anda bercakap dengan papan:

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 Ia meminta kata laluan komputer anda. Tiada apa-apa dipaparkan
   semasa anda menaip; itu normal, taip sahaja dan tekan **Enter**.

3. Pasang `esptool` dan `unzip`, satu baris pada satu masa:

   ```bash
   sudo apt update
   ```

   ```bash
   sudo apt install pipx unzip
   ```

   ```bash
   pipx install esptool
   ```

   ```bash
   pipx ensurepath
   ```

4. **Log keluar dari komputer anda dan log masuk semula**, kemudian buka
   Terminal sekali lagi.

</details>

✅ Semak esptool berfungsi:

```bash
esptool version
```

Ia mesti memaparkan **5.3 atau lebih baharu**. Versi lama tidak boleh
flash cip yang lebih baharu: 🍎 `brew upgrade esptool`,
🐧 `pipx upgrade esptool`.

Seterusnya: [SeedSigner 2: sambungkan papan dan semak cip anda](#ss-chip).

<a id="ss-chip"></a>

## 💻 SeedSigner 2: sambungkan papan dan semak cip anda

Papan ini datang dengan salah satu daripada dua versi cip, **v1.x** pada
papan lama atau **v3.x** pada papan baharu, dan setiap satu perlukan
binaan SeedSigner sendiri. Halaman ini memberitahu yang mana anda ada.

1. Pasang kabel USB-C ke port pada papan yang bertanda **UART**, dan
   hujung satu lagi ke komputer anda.

2. Jalankan baris untuk komputer anda:

   🍎 Mac:

   ```bash
   esptool -p $(ls /dev/cu.usbmodem* | head -n 1) chip-id
   ```

   🐧 Linux:

   ```bash
   esptool -p $(ls /dev/ttyACM* | head -n 1) chip-id
   ```

3. Cari baris yang berbunyi `ESP32-P4 (revision ...)`:

   - `revision v1.3` atau mana-mana **v1**: anda ada cip **v1.x**.
   - `revision v3.0`, `v3.2` atau mana-mana **v3**: anda ada cip **v3.x**.

   Catatkan: halaman seterusnya memerlukannya.

Seterusnya: [SeedSigner 3: muat turun binaan untuk cip anda](#ss-download).

<a id="ss-download"></a>

## 💻 SeedSigner 3: muat turun binaan untuk cip anda

Gunakan **hanya binaan untuk cip anda** daripada SeedSigner 2. Yang satu
lagi tidak akan bermula.

<details open>
<summary><b>1️⃣ Cip saya v1.x</b></summary>

Klik untuk memuat turun
[seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip](https://github.com/DIYbitcoin/guides/releases/download/seedsigner-p4-35-touchfix/seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip)
ke folder **Downloads** anda.

Ia termasuk **pembetulan sentuhan** supaya leretan menatal menu dan bukan
menekan butang. Pembetulan ini masih
[pull request terbuka](https://github.com/kdmukai/esp-board-common/pull/21),
belum menjadi sebahagian SeedSigner. Diuji pada papan v1.3.

SHA256 zip:
`d372f345a1895f2e90ed5d86cf473136bd69948ec2384de7c8dd9313eea169e5`

</details>

<details open>
<summary><b>3️⃣ Cip saya v3.x</b></summary>

Binaan Keith datang dari GitHub, dan anda perlukan akaun GitHub percuma
untuk memuat turunnya.

1. Log masuk di [github.com/login](https://github.com/login), atau buat
   akaun di sana dahulu.
2. Buka
   [binaan terkini SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder/actions/workflows/build-firmware.yml?query=branch%3Amain+is%3Asuccess).
3. Klik binaan **paling atas** dalam senarai, iaitu yang terbaharu.
4. Tatal ke bawah ke **Artifacts** dan klik
   **flashable-dist-waveshare-esp32-p4-35** untuk memuat turunnya ke
   folder **Downloads** anda.

   ⚠️ Pastikan namanya berakhir dengan **35**. Yang `43` untuk papan 4.3
   inci, dan fail di halaman Releases Keith juga untuk 4.3 inci. Kedua-duanya
   tidak bermula pada papan ini.

</details>

Seterusnya: [SeedSigner 4: nyahzip dan flash](#ss-flash).

<a id="ss-flash"></a>

## 💻 SeedSigner 4: nyahzip dan flash

1. Masuk ke Downloads:

   ```bash
   cd ~/Downloads
   ```

2. Nyahzip dan masuk ke folder, dengan dua baris untuk cip **anda**:

   **v1.x:**

   ```bash
   unzip seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip
   ```

   ```bash
   cd seedsigner-waveshare-p4-35-pre-rev3-touchfix
   ```

   **v3.x:**

   ```bash
   unzip flashable-dist-waveshare-esp32-p4-35.zip -d seedsigner-v3
   ```

   ```bash
   cd seedsigner-v3
   ```

   💡 Pada Mac, Safari mungkin sudah menyahzipnya. Jika `unzip` kata ia
   tidak jumpa fail itu, langkau baris itu dan jalankan baris `cd` sahaja.

3. Flash, dengan baris untuk komputer anda:

   🍎 Mac:

   ```bash
   esptool --chip esp32p4 -p $(ls /dev/cu.usbmodem* | head -n 1) write-flash @flash_args
   ```

   🐧 Linux:

   ```bash
   esptool --chip esp32p4 -p $(ls /dev/ttyACM* | head -n 1) write-flash @flash_args
   ```

   Ia mengambil kira-kira 4 minit, jadi jangan cabut papan. Beberapa
   amaran `Deprecated` pada permulaan tidak mengapa.

   ✅ Apabila selesai, papan dimulakan semula ke dalam SeedSigner. 🎉

4. Cuba dengan data ujian: buka [testqrs.com](https://testqrs.com) pada
   komputer anda dan halakan kamera papan ke kod QR di situ. Perkataan
   benihnya awam, jadi gunakan untuk latihan sahaja.

Seterusnya: [🔋 pasang bateri](#battery), atau
[🩺 penyelesaian masalah](#troubleshooting) jika ada yang tidak kena.

<a id="battery"></a>

## 🔋 Pasang bateri

Papan ini datang dengan bateri kecil dan pemutar skru kecil, supaya ia
boleh berjalan tanpa kabel.

1. Di belakang sarung, tanggalkan empat penutup kecil dengan kuku anda.
   Skru ada di bawahnya.
2. Buka empat skru dengan pemutar skru dan angkat bahagian belakang
   sarung.
3. Pasang palam bateri ke soket bateri di bahagian bawah kanan papan. Ia
   hanya muat satu arah, jadi jika tidak masuk, terbalikkan. Jangan paksa.
4. ✅ Lampu hijau sepatutnya menyala di sebelah butang kuasa.
5. Letak semula bahagian belakang sarung dan skru rapat. Sarung tertutup
   seperti biasa dengan bateri di dalam.

![Papan Waveshare 3.5 inci dengan sarung terbuka dan bateri dipasang](images/battery.jpg)

<a id="troubleshooting"></a>

## 🩺 Penyelesaian masalah

| Anda nampak | Apa yang perlu dibuat |
|---|---|
| Tetingkap pelayar tidak menyenaraikan papan untuk dipilih | Gunakan **Chrome** atau **Brave**, port **UART** dan kabel data. 🐧 Buat langkah Linux di halaman [Kern](#linux-kern) atau [KISS Signer](#linux-kiss). |
| `port is busy` atau `could not open port` | Tutup tab pemancar web dan Terminal lain yang menggunakan papan, kemudian cuba lagi. |
| 🍎 `no matches found: /dev/cu.usbmodem*` atau 🐧 `No such file or directory` | Komputer tidak nampak papan: gunakan port **UART** dan kabel yang anda tahu membawa data. |
| 🐧 `Permission denied`, atau pelayar tidak boleh membuka papan | Buat langkah Linux, termasuk log keluar dan log masuk semula. |
| `Failed to connect` | Tutup apa-apa yang menggunakan papan, cabut, pasang semula dan cuba lagi. |
| 🍎 `brew: command not found` | Jalankan arahan **Next steps** yang Homebrew paparkan apabila selesai dipasang. |
| 🐧 `esptool: command not found` | Log keluar dan log masuk semula, kemudian buka Terminal baharu. |
| `requires chip revision in range` | Binaan tidak sepadan dengan cip anda: ulang [SeedSigner 2](#ss-chip) dan muat turun binaan yang satu lagi. |
| `flash_args` tidak dijumpai | Anda tiada dalam folder yang dinyahzip: ulang langkah 2 [SeedSigner 4](#ss-flash). |
| Tiada apa-apa pada skrin selepas flash | Cabut, tunggu 3 saat, pasang semula. Semak binaan sepadan dengan cip anda. |
| `esptool` tidak boleh flash cip | Ia mesti 5.3 atau lebih baharu: 🍎 `brew upgrade esptool`, 🐧 `pipx upgrade esptool`. |

## 🙏 Penghargaan

- [Kern](https://github.com/odudex/Kern) oleh odudex.
- [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder) oleh kdmukai.
- [KISS Signer](https://github.com/DIYbitcoin/kiss-signer) oleh DIYbitcoin.
