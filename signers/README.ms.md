# 📟 Signer DIY: SeedSigner, Kern dan KISS Signer

Signer ialah peranti kecil luar talian yang menyimpan kunci bitcoin anda dan
menandatangani transaksi, jadi kunci anda tidak pernah menyentuh komputer
atau telefon. Panduan ini memasang ("flash") signer sumber terbuka pada
[Waveshare ESP32-P4-WIFI6-Touch-LCD-3.5](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-3.5.htm),
papan skrin sentuh 3.5 inci dengan kamera dan slot microSD. Tidak perlu memateri.

| Signer | Cara flash | Status |
|---|---|---|
| [Kern](https://github.com/odudex/Kern) | Dalam pelayar web anda | Versi ujian |
| [KISS Signer](https://github.com/DIYbitcoin/kiss-signer) | Dalam pelayar web anda | Beta |
| [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder) | Dalam Terminal | Versi ujian |

📷 **Cara menggunakannya:** signer tidak pernah bersambung ke internet. Anda
menggandingkannya dengan aplikasi dompet seperti Sparrow dengan mengimbas
kod QR menggunakan kameranya, dan menandatangani transaksi dengan cara yang sama.

⚠️ **Ketiga-tiganya ialah versi awal pada papan ini. Gunakan perkataan benih
latihan dan testnet (bitcoin latihan yang tiada nilai) atau jumlah yang sangat
kecil sahaja, jangan sekali-kali gunakan simpanan sebenar anda.**

## 🔌 Langkah 1: sambungkan papan

Sambungkan kabel USB-C ke port yang bertanda **UART**.

💡 Gunakan kabel yang anda tahu boleh membawa data. Kabel yang hanya untuk mengecas memberi
kuasa kepada papan, tetapi komputer tidak nampak apa-apa langsung.

<a id="step-2"></a>

## 💻 Langkah 2: sediakan komputer anda

🌐 Untuk **Kern** dan **KISS Signer**, anda flash melalui pemasang web dalam
**Chrome** atau **Brave**, jadi pelayar itu sahaja yang anda perlukan (pada
Linux, lakukan juga langkah 2 dalam kotak Linux di bawah).

💻 Untuk **SeedSigner**, anda flash dari Terminal dengan `esptool`: pasang
alat itu di bawah.

<details>
<summary><b>🍎 Saya guna Mac</b></summary>

1. Buka **Terminal**: tekan **Cmd + Space**, taip `Terminal`, tekan
   **Enter**.

2. Pasang **Homebrew**, pemasang aplikasi Mac untuk Terminal. Langkau
   langkah ini jika anda sudah memilikinya (`brew --version` memaparkan
   satu versi).

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 Ia meminta kata laluan Mac anda. Tiada apa-apa yang dipaparkan
   semasa anda menaip; itu perkara biasa, taip sahaja dan tekan **Enter**.

   ⚠️ Apabila selesai, ia memaparkan **Next steps** dengan dua atau tiga
   arahan. Salin dan jalankan arahan itu juga, jika tidak Mac tidak akan
   menjumpai `brew`.

3. Pasang esptool:

   ```bash
   brew install esptool
   ```

</details>

<details>
<summary><b>🐧 Saya guna Linux</b></summary>

1. Buka **Terminal**: tekan **Ctrl + Alt + T**, atau cari **Terminal**
   dalam aplikasi anda.

2. Benarkan pengguna anda berhubung dengan papan (diperlukan untuk
   ketiga-tiga signer, termasuk Kern dalam pelayar):

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 Ia meminta kata laluan komputer anda. Tiada apa-apa yang dipaparkan
   semasa anda menaip; itu perkara biasa, taip sahaja dan tekan **Enter**.

3. **Log keluar dari komputer anda dan log masuk semula**, supaya perubahan
   itu berkuat kuasa.

4. Untuk SeedSigner, pasang `esptool` dan `unzip`. Buka Terminal sekali
   lagi dan jalankan:

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

5. **Tutup Terminal dan buka yang baharu**, supaya ia menjumpai `esptool`.

</details>

✅ Untuk SeedSigner, semak esptool berfungsi:

```bash
esptool version
```

Ia mesti memaparkan **5.3 atau lebih baharu**.

<a id="step-3"></a>

## 🧬 Langkah 3: kenali cip anda

Papan 3.5 inci ini datang dengan salah satu daripada dua versi cip ESP32-P4:
**v1.x** pada papan lama, atau **v3.x** pada papan baharu, jadi papan yang
dibeli baru-baru ini mungkin mempunyai v3.x. Setiap signer memerlukan binaan
yang dibuat untuk cip anda: binaan v1.x tidak akan berjalan pada cip v3.x,
dan begitu juga sebaliknya.

| Signer | Cip v1.x | Cip v3.x |
|---|---|---|
| Kern | Pemasang web, atau keluaran rasmi | Pemasang web sahaja (versi ujian) |
| KISS Signer | Halaman pemasangan (beta bertandatangan) | Pemasang ujian (versi ujian) |
| SeedSigner | Binaan pembaikan sentuhan | Binaan ujian daripada builder |

🌐 **Kern** dan **KISS Signer:** tidak perlu menyemak, bahagian masing-masing
di bawah menunjukkan caranya.

💻 **SeedSigner:** semak sekarang dengan `esptool` ([Langkah 2](#step-2)),
supaya anda memuat turun binaan yang betul:

1. Beritahu Terminal di mana papan itu berada. Ini menyimpannya sebagai `$PORT`.

   🍎 Mac:

   ```bash
   PORT=$(ls /dev/cu.usbmodem* | head -n 1)
   ```

   🐧 Linux:

   ```bash
   PORT=$(ls /dev/ttyACM* | head -n 1)
   ```

2. Semak cip:

   ```bash
   esptool -p $PORT chip-id
   ```

   ✅ Ia memaparkan `ESP32-P4 (revision v1.3)` atau seumpamanya: itu cip
   **v1.x**. `v3.0` atau lebih tinggi ialah cip **v3.x**.

## Kern

1. Buka [pemasang web Kern](https://odudex.github.io/Kern/flash/) dalam
   **Chrome** atau **Brave** (Safari dan Firefox tidak boleh flash).
2. Sambungkan papan dan flash **Latest CI Build**. Pemasang itu membaca
   cip anda dan memilih binaan v1.x atau v3.x dengan sendirinya.
3. ✅ Cabut, tunggu 3 saat, sambungkan semula.

📦 [Keluaran rasmi Kern](https://github.com/odudex/Kern/releases) hanya
menyokong cip **v1.x**.

## KISS Signer

1. Buka [halaman pemasangan](https://diybitcoin.github.io/kiss-signer/) dalam
   **Chrome** atau **Brave**, sambungkan papan dan flash.
2. ✅ Cabut, tunggu 3 saat, sambungkan semula.
3. Skrin kekal hitam? Papan anda mempunyai cip v3.x yang lebih baharu: flash
   sekali lagi dari [pemasang ujian](https://diybitcoin.github.io/kiss-signer/test/)
   sebagai ganti.

⚠️ Pemasang ujian mempunyai versi ujian yang tidak bertandatangan, belum
dicuba pada papan v3.x yang sebenar.

## SeedSigner

SeedSigner untuk papan ini ialah versi awal, yang anda flash dari Terminal
dengan `esptool` ([Langkah 2](#step-2)).

### 1️⃣ Muat turun binaan untuk cip anda

Simpan dalam folder **Downloads** anda:

- **v1.x**:
  [seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip](https://github.com/DIYbitcoin/guides/releases/download/seedsigner-p4-35-touchfix/seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip)
- **v3.x**: `flashable-dist-waveshare-esp32-p4-35` dari
  [binaan Actions](https://github.com/kdmukai/seedsigner-micropython-builder/actions)
  builder (perlu log masuk GitHub)

<details>
<summary><b>👆 Tentang binaan v1.x</b></summary>

Ia mengandungi **pembaikan sentuhan** supaya leretan menatal menu dan
bukannya menekan butang. Pembaikan ini masih dalam
[pull request yang terbuka](https://github.com/kdmukai/esp-board-common/pull/21),
belum lagi menjadi sebahagian daripada SeedSigner. Diuji pada papan v1.3.

SHA256 fail zip itu:
`d372f345a1895f2e90ed5d86cf473136bd69948ec2384de7c8dd9313eea169e5`

</details>

<a id="seedsigner-unzip"></a>

### 2️⃣ Nyahzip

Masuk ke Downloads:

```bash
cd ~/Downloads
```

Kemudian jalankan dua baris untuk binaan **anda**.

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

### 3️⃣ Flash

💡 Jika anda telah membuka Terminal baharu sejak [Langkah 3](#step-3),
jalankan baris `PORT=` dalam langkah itu sekali lagi dahulu.

```bash
esptool --chip esp32p4 -p $PORT write-flash @flash_args
```

✅ Apabila selesai, papan itu dimulakan semula dan membuka SeedSigner. 🎉

<a id="battery"></a>

## 🔋 Pasang bateri

Papan ini datang dengan bateri kecil dan pemutar skru kecil, jadi ia boleh
berfungsi tanpa kabel.

1. Di bahagian belakang bekas, tanggalkan empat penutup kecil dengan kuku
   anda. Skru berada di bawahnya.
2. Buka empat skru itu dengan pemutar skru dan angkat bahagian belakang
   bekas.
3. Sambungkan palam bateri ke soket bateri di bahagian bawah kanan papan.
   Ia hanya muat dalam satu arah, jadi jika tidak masuk, terbalikkan palam
   itu. Jangan paksa.
4. ✅ Lampu hijau sepatutnya menyala di sebelah butang kuasa.
5. Pasang semula bahagian belakang bekas dan skru hingga tertutup. Bekas
   itu tertutup seperti biasa dengan bateri di dalamnya.

![Papan Waveshare 3.5 inci dengan bekas terbuka dan bateri disambungkan](images/battery.jpg)

## 🩺 Penyelesaian masalah

| Anda nampak | Apa yang perlu dibuat |
|---|---|
| Pemasang web tidak menunjukkan papan untuk dipilih | Gunakan **Chrome** atau **Brave**, port **UART** dan kabel data. 🐧 Lakukan [Langkah 2](#step-2) pada Linux. |
| `port is busy` atau `could not open port` | Tutup tab pemasang web dan mana-mana Terminal lain yang menggunakan papan, kemudian cuba lagi. |
| Komputer tidak nampak papan, atau 🍎 `no matches found: /dev/cu.usbmodem*` / 🐧 `No such file or directory` selepas baris `PORT=` | Gunakan port **UART** dan kabel yang anda tahu boleh membawa data. |
| 🐧 `Permission denied`, atau pelayar tidak boleh membuka papan | Lakukan [Langkah 2](#step-2) pada Linux, termasuk log keluar dan log masuk semula. |
| 🍎 `brew: command not found` | Jalankan arahan **Next steps** yang dipaparkan oleh Homebrew selepas ia selesai dipasang. |
| 🐧 `esptool: command not found` | Tutup Terminal dan buka yang baharu. |
| `flash_args` tidak dijumpai | Anda tidak berada dalam folder yang telah dinyahzip: ulang [langkah 2 SeedSigner](#seedsigner-unzip). |
| Tiada apa-apa pada skrin selepas flash | Cabut, tunggu 3 saat, sambungkan semula. Semak binaan itu sepadan dengan cip anda. |
| `esptool` tidak boleh flash cip | Ia mesti versi 5.3 atau lebih baharu: 🍎 `brew upgrade esptool`, 🐧 `pipx upgrade esptool`. |

## 🙏 Penghargaan

- [Kern](https://github.com/odudex/Kern) oleh odudex.
- [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder) oleh kdmukai.
- [KISS Signer](https://github.com/DIYbitcoin/kiss-signer) oleh DIYbitcoin.
