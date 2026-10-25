# 💳 Flash javacard: Satochip, Satodime, SeedKeeper dan Keycard

Javacard mempunyai **elemen selamat**: cip yang dibina untuk menyimpan
rahsia seperti kunci bitcoin anda dengan selamat, walaupun kad itu jatuh
ke tangan orang lain. Anda boleh memasang aplikasi kecil padanya, yang dipanggil
**applet**. Panduan ini memasang salah satu daripada empat applet bitcoin
sumber terbuka pada javacard J3H145 yang kosong:

- **Satochip:** dompet bitcoin DIY yang anda gunakan dengan Sparrow Wallet
- **Satodime:** kad pembawa, jadi sesiapa yang memegang kad itu memiliki
  bitcoin di dalamnya, seperti wang tunai
- **SeedKeeper:** sandaran yang disulitkan dan dikunci dengan PIN, untuk
  perkataan benih dan passphrase anda, kata laluan, deskriptor dompet dan
  rahsia lain
- **Keycard:** satu lagi dompet bitcoin DIY yang anda gunakan dengan
  Sparrow Wallet

Applet ini datang daripada [Satochip](https://satochip.io) dan
[Keycard](https://keycard.tech), dan panduan ini mengikut panduan
[Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY).

![Komputer, pembaca kad, kad dan empat applet](images/00-overview.svg)

## 🧰 Apa yang anda perlukan

- 💳 Javacard **J3H145**. Jika anda mahu membelinya, inilah kad yang
  digunakan untuk panduan ini: [J3H145 di Alibaba](https://www.alibaba.com/x/B2cDpJ?ck=pdp)
- 🔌 **Pembaca kad pintar USB**: kotak kecil yang anda sambungkan ke
  komputer, lalu masukkan kad ke dalamnya, bahagian cip dahulu. Jangan
  guna **ACS ACR122U**, pembaca NFC yang anda sentuhkan kad padanya:
  [panduan Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY#smartcard-readers)
  memberi amaran bahawa ia tidak boleh diharap untuk memasang applet dan
  boleh merosakkan kad. Ia sesuai untuk menggunakan kad selepas applet
  dipasang.
- 🛡️ Anda ada [ShieldSigner](https://github.com/3rdIteration/seedsigner)?
  Ia juga boleh memasang applet ini pada kad, sebagai ganti komputer dan
  pembaca kad.
- 💻 Komputer **Mac**, **Linux** (Debian, Ubuntu atau Raspberry Pi OS) atau
  **Windows**
- 🌐 Sambungan internet untuk muat turun

## 🛠️ Langkah 1: sediakan komputer anda (sekali sahaja)

Ini memasang **Java** dan **GlobalPlatformPro**, iaitu arahan `gp` yang
meletakkan applet pada kad.

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

3. Pasang Java:

   ```bash
   brew install --cask temurin
   ```

4. Pasang GlobalPlatformPro:

   ```bash
   brew install martinpaljak/brew/gppro
   ```

</details>

<details>
<summary><b>🐧 Saya guna Linux</b></summary>

1. Buka **Terminal**: tekan **Ctrl + Alt + T**, atau cari **Terminal**
   dalam aplikasi anda.

2. Pasang Java, git dan perkhidmatan kad pintar (`pcscd`):

   ```bash
   sudo apt update
   ```

   🔒 Ia meminta kata laluan komputer anda. Tiada apa-apa yang dipaparkan
   semasa anda menaip; itu perkara biasa, taip sahaja dan tekan **Enter**.

   ```bash
   sudo apt install default-jre pcscd git
   ```

3. Muat turun fail [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY),
   yang mengandungi GlobalPlatformPro sebagai `gp.jar`:

   ```bash
   git clone https://github.com/3rdIteration/Satochip-DIY.git ~/Satochip-DIY
   ```

4. Berikan nama pendek `gp`, supaya setiap arahan dalam panduan ini
   berfungsi seperti yang ditulis:

   ```bash
   echo "alias gp='java -jar ~/Satochip-DIY/gp.jar'" >> ~/.bashrc
   ```

5. **Tutup Terminal dan buka yang baharu**, supaya ia mengenali nama
   baharu itu.

</details>

<details>
<summary><b>🪟 Saya guna Windows</b></summary>

⚠️ Ia belum diuji pada Windows.

1. Pasang Java: muat turun **Temurin** dari <https://adoptium.net> dan
   pasangkannya dengan pilihan lalai.

2. Muat turun
   [gp.exe](https://github.com/3rdIteration/Satochip-DIY/raw/main/gp.exe)
   dari [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) ke
   dalam folder **Downloads** anda.

3. Buka **Command Prompt**: tekan kekunci **Windows**, taip `cmd`, tekan
   **Enter**. Ia tidak perlu dijalankan sebagai pentadbir.

4. Masuk ke folder Downloads anda:

   ```bat
   cd %USERPROFILE%\Downloads
   ```

5. Di mana sahaja panduan ini menyebut `gp`, taip `gp.exe` sebagai ganti,
   contohnya `gp.exe --info`. Di mana sahaja ia menyebut `cd ~/Downloads`,
   gunakan baris dari langkah 4.

</details>

✅ Semak ia berfungsi:

```bash
gp --version
```

Ia berfungsi jika ia memaparkan beberapa baris yang bermula dengan `#` dan
tiada `command not found`.

💡 Java memaparkan beberapa baris yang bermula dengan `WARNING:` setiap kali
`gp` dijalankan. Itu perkara biasa dan anda boleh mengabaikannya.

## 🔌 Langkah 2: pastikan pembaca dapat mengesan kad

Sambungkan pembaca, masukkan kad (bahagian cip dahulu) dan jalankan:

```bash
gp --info
```

✅ Jika anda nampak baris `CPLC` dan `Global Platform card`, pembaca dan kad
sedang berhubung:

![Output gp --info](images/01-gp-info.svg)

Sekarang lihat apa yang sudah ada pada kad:

```bash
gp --list
```

Kad kosong hanya menunjukkan apa yang sudah ada padanya dari kilang: pengurus kad
(`ISD`) dan satu pakej (`A0000001515350`). Jangan usik kedua-duanya.

![gp --list pada kad kosong](images/02-gp-list-blank.svg)

### 🛑 Berhenti jika anda nampak "Card cryptogram invalid"

![Amaran Card cryptogram invalid](images/07-gp-cryptogram-stop.svg)

⚠️ Ini bermakna kad itu telah dikunci dengan kunci yang anda tidak miliki.
**Jangan jalankan arahan itu lagi**: selepas beberapa cubaan yang salah,
kad akan mengunci dirinya buat selama-lamanya. Minta kuncinya daripada
orang yang menjual kad itu kepada anda.

## 📥 Langkah 3: muat turun fail applet

Muat turun fail untuk applet yang anda mahu ke dalam folder **Downloads**
anda. Pada setiap halaman, klik nama fail di bawah **Assets** untuk
memuat turunnya.

| Applet | Fail untuk dimuat turun | Disediakan dengan |
|---|---|---|
| Satochip | [SatoChip-v0.15-0.1.cap](https://github.com/Toporin/SatochipApplet/releases/tag/v0.15-0.1) | Sparrow Wallet |
| Satodime | [Satodime-0.1-0.2.cap](https://github.com/Toporin/Satodime-Applet/releases/tag/v0.1-0.2) | Sato-Tools |
| SeedKeeper | [SeedKeeper-v0.2-0.1.cap](https://github.com/Toporin/Seedkeeper-Applet/releases/tag/v0.2-0.1) | Sato-Tools |
| Keycard | [keycard_v3.2.cap](https://github.com/keycard-tech/status-keycard/releases/tag/3.2) | Sparrow Wallet |

<details>
<summary><b>💡 Mengapa versi ini dan bukan yang terbaharu?</b></summary>

- **Satochip v0.15, bukan v0.16:** v0.16 (Oktober 2026) mengubah cara kad
  melaraskan kunci taproot, dan Sparrow 2.5.5 masih menghantar format lama,
  jadi dompet taproot dalam Sparrow tidak boleh menandatangani dengan kad
  v0.16. Native SegWit berfungsi pada kedua-duanya.
- **Satodime v0.1-0.2** ialah keluaran stabil yang terkini; binaan v0.2
  masih bertanda beta.
- **Keycard 3.2, bukan 4.0:** Keycard 4.0 (September 2026) mengubah cara
  kad dan komputer menyediakan sambungan yang disulitkan antara keduanya,
  dan Sparrow 2.5.5 masih menggunakan cara lama.

Gunakan versi yang lebih baharu apabila Sparrow sudah serasi dengannya.

</details>

<details>
<summary><b>🔍 Pilihan: pastikan fail itu tidak diusik</b></summary>

Masuk ke folder Downloads anda:

```bash
cd ~/Downloads
```

🍎 Mac:

```bash
shasum -a 256 SatoChip-v0.15-0.1.cap
```

🐧 Linux:

```bash
sha256sum SatoChip-v0.15-0.1.cap
```

🪟 Windows:

```bat
certutil -hashfile SatoChip-v0.15-0.1.cap SHA256
```

Kod panjang yang dipaparkan mesti sepadan dengan kod pada halaman keluaran:

- `SatoChip-v0.15-0.1.cap`:
  `83b117ebe047acdb1a171196c709016329ae0d221f9c4d0c606076a634ecdc2a`
- `keycard_v3.2.cap`:
  `9ed83c56e64cfb00d732cab4705e6f339fa1f039507ad86de7028bb3056675e8`

</details>

<a id="step-4"></a>

## ⚡ Langkah 4: letakkan applet pada kad

🔄 Mahu menukar applet pada kad anda kepada applet lain? Buang yang lama
dahulu, kemudian pasang yang baharu: lihat
[Menukar kepada applet lain](#switching) di bawah.

Masuk ke folder Downloads anda, tempat fail itu berada:

```bash
cd ~/Downloads
```

Kemudian jalankan **hanya baris untuk applet anda**:

Satochip:

```bash
gp --install SatoChip-v0.15-0.1.cap
```

Satodime:

```bash
gp --install Satodime-0.1-0.2.cap
```

SeedKeeper:

```bash
gp --install SeedKeeper-v0.2-0.1.cap
```

Keycard (tetapan tambahan itu menjadikannya dompet yang dicari oleh Sparrow):

```bash
gp --install keycard_v3.2.cap --applet A000000804000101 --create A00000080400010101
```

✅ Ia berakhir dengan baris `loaded:`:

![Output gp --install](images/03-gp-install-satochip.svg)

![gp --install untuk Keycard](images/09-gp-install-keycard.svg)

✅ Jalankan `gp --list` sekali lagi. Baris baharu yang bermula dengan `APP`
dan berakhir dengan `SELECTABLE` bermakna applet itu sudah dipasang dan
sedia:

![gp --list dengan Satochip dipasang](images/04-gp-list-satochip.svg)

Sekarang pergi ke langkah persediaan untuk applet anda:

- Satochip: [Langkah 5a](#step-5a)
- SeedKeeper atau Satodime: [Langkah 5b](#step-5b)
- Keycard: [Langkah 5c](#step-5c)

<a id="step-5a"></a>

## Langkah 5a: sediakan Satochip dalam Sparrow

👛 Dapatkan Sparrow Wallet untuk komputer anda dari
<https://sparrowwallet.com/download/>.

Anda menetapkan PIN dan memuatkan 12 perkataan benih terus dari Sparrow;
aplikasi Satochip tidak diperlukan. Biarkan kad dalam pembaca sepanjang masa.

1. Cipta dompet baharu dalam Sparrow dan namakannya. Di bawah **Keystores**,
   pilih **Connected Hardware Wallet** dan tekan **Scan...**

   ![Connected Hardware Wallet, Scan](images/sparrow-1-scan.webp)

2. Sparrow menjumpai **Satochip**. Tekan **Import Keystore**.

   ![Sparrow menjumpai Satochip](images/sparrow-2-found.webp)

3. Sparrow meminta anda **Choose a PIN code**. Taip satu PIN.

   ![Choose a PIN code](images/sparrow-3-choose-pin.webp)

4. Sparrow menyatakan **Card not initialized**. Taip PIN yang sama sekali
   lagi dalam kotak dan tekan **Initialize**.

   ![Card not initialized, Initialize](images/sparrow-4-initialize.webp)

5. Tetingkap **Satochip Seed Words** dibuka. Tekan **Generate New** untuk
   set 12 perkataan benih yang baharu, atau taip 12 perkataan benih yang
   anda sudah ada. ✍️ **Tulis 12 perkataan benih itu di atas kertas**,
   kemudian tekan **OK**. Sparrow memuatkannya ke dalam kad, tanpa
   passphrase.

   💡 Sparrow menjana 12 perkataan benih ini pada komputer anda, jadi untuk
   simpanan sebenar, gunakan komputer yang anda percayai.

   ![Satochip Seed Words](images/sparrow-5-seed-words.webp)

6. ✅ **Card Initialized**. Tekan **OK**.

   ![Card Initialized](images/sparrow-6-initialized.webp)

7. Kembali pada baris Satochip, tekan **Import Keystore** sekali lagi.

   ![Import Keystore sekali lagi](images/sparrow-7-import-keystore.webp)

8. 🎉 Dompet itu kini menunjukkan Satochip anda, dengan cap jarinya: kod
   pendek yang mengenal pasti kunci anda. Tekan **Apply** untuk menyimpan
   dompet itu.

   ![Dompet yang siap dalam Sparrow](images/sparrow-8-wallet.webp)

🔢 Sparrow memberi kad itu **5 cubaan PIN**. Dari keystore, anda juga
boleh menukar PIN kemudian dengan **Change Pin**.

<a id="step-5b"></a>

## Langkah 5b: sediakan SeedKeeper atau Satodime dalam Sato-Tools

🧰 Dapatkan Sato-Tools untuk komputer anda dari
<https://github.com/Toporin/Satochip-Utils/releases> (panduan ini
menggunakan 0.4.0-beta).

Buka Sato-Tools dengan kad di dalam pembaca dan tekan **Let's go!**

![Skrin alu-aluan Sato-Tools](images/satotools-0-welcome.webp)

💡 **Jika Sato-Tools tidak nampak kad yang baru anda flash, tutup aplikasi
itu dan buka semula.**

### SeedKeeper

1. Sato-Tools menyatakan **Your SeedKeeper is connected**. Tekan
   **Setup my card**.

   ![Your SeedKeeper is connected](images/satotools-seedkeeper-1-connected.png)

2. Taip PIN dalam **New PIN code** dan sekali lagi dalam **Confirm PIN code**
   (4 hingga 16 aksara), kemudian tekan **Save PIN**.

   ![Setup my card](images/satotools-seedkeeper-2-setup-pin.png)

3. ✅ **Your card is now setup!** Tekan **Ok**.

   ![Your card is now setup](images/satotools-seedkeeper-3-success.webp)

4. 🎉 Menu kini mempunyai **My secrets**, **Generate secret**, **Import secret**,
   **Backup card** dan **Card logs**. SeedKeeper anda sedia untuk menyimpan
   perkataan benih, passphrase, kata laluan dan deskriptor dompet.

   ![SeedKeeper sedia](images/satotools-seedkeeper-4-ready.png)

### Satodime

1. Sato-Tools menyatakan **Your Satodime is connected**, dengan tiga peti
   besi: **Vault #0**, **Vault #1** dan **Vault #2**.

   ![Your Satodime is connected](images/satotools-satodime-1-connected.webp)

2. **Overview** menunjukkan ketiga-tiga peti besi sebagai **uninitialized**:
   belum ada apa-apa yang dimeterai di dalamnya.

   ![Peti besi saya](images/satotools-satodime-2-vaults.png)

3. **About** menunjukkan versi applet dan **Card has no owner**.
   **Take ownership** menjadikan komputer ini pemilik kad, sesuatu yang anda
   perlukan sebelum memeterai peti besi.

   ![About, Take ownership](images/satotools-satodime-3-about.png)

4. 🎉 Tekan **Take ownership**. About kini menyatakan **You are the owner**,
   dan butang itu bertukar kepada **Transfer ownership**, yang anda gunakan
   apabila anda menyerahkan kad itu kepada orang lain.

💡 **Memeterai** peti besi menjana alamat bitcoin untuknya, dengan kunci
persendiriannya tersembunyi di dalam kad. **Membuka meterai** menunjukkan kunci
persendirian supaya bitcoin itu boleh dipindahkan. Sebelum anda menerima
Satodime sebagai bayaran, semak peti besinya masih bermeterai.

### 📱 Pada telefon anda

SeedKeeper dan Satodime juga mempunyai aplikasi telefon. Sentuhkan kad pada
bahagian belakang telefon yang ada NFC untuk menggunakannya di situ.

### ❓ Mengapa ia menyatakan "Genuine: NO"?

Kad yang dibeli daripada Satochip datang dengan sijil daripada Satochip yang
dimuatkan di kilang, dan **Genuine** menyemak sijil itu. Kad DIY yang anda
flash sendiri tidak mempunyai sijil sedemikian, jadi ia sentiasa menunjukkan
**Genuine: [NO]**. Applet itu berfungsi sama sahaja.

<a id="step-5c"></a>

## Langkah 5c: sediakan Keycard dalam Sparrow

👛 Dapatkan Sparrow Wallet untuk komputer anda dari
<https://sparrowwallet.com/download/> jika anda belum memilikinya.

Keycard disediakan sama seperti Satochip, dengan dua perbezaan: PIN mestilah
**tepat 6 digit**, dan kad itu hanya membenarkan **3 PIN yang salah**.

1. Cipta dompet baharu, pilih **Connected Hardware Wallet**, tekan
   **Scan...** Sparrow menjumpai **Keycard**. Tekan **Import Keystore**.

   ![Sparrow menjumpai Keycard](images/keycard-1-found.webp)

2. Pilih PIN yang **tepat 6 digit**. Jika lebih pendek atau lebih panjang,
   anda akan nampak **PIN length must be between 6 and 6 characters**.

   ![PIN mesti 6 digit](images/keycard-2-pin-error.webp)

3. Taip PIN yang sama sekali lagi dan tekan **Initialize**. Tetingkap
   perkataan benih yang sama seperti Satochip dibuka ([Langkah 5a](#step-5a),
   nombor 5): tekan **Generate New** atau taip perkataan anda sendiri,
   ✍️ **tulis 12 perkataan benih itu**, kemudian tekan **OK**.
   ✅ **Card Initialized**: tekan **OK**.

   ![Card Initialized](images/keycard-3-initialized.webp)

4. 🎉 Tekan **Import Keystore** sekali lagi. Dompet itu kini menunjukkan
   Keycard anda, dengan cap jarinya. Tekan **Apply** untuk menyimpan dompet
   itu.

   ![Dompet Keycard yang siap dalam Sparrow](images/keycard-4-wallet.webp)

⚠️ **12 perkataan benih anda ialah satu-satunya cara untuk memulihkannya.**
Selepas 3 PIN yang salah, Keycard akan terkunci, dan Sparrow tidak memberi
anda cara untuk membukanya. Jika itu berlaku, buang Keycard, pasang semula
dan taip semula 12 perkataan benih anda dalam Sparrow.

<a id="switching"></a>

## 🔄 Menukar kepada applet lain

⚠️ **Membuang applet akan memadam semua yang disimpan di dalamnya**,
termasuk benih pada Satochip atau Keycard dan rahsia pada SeedKeeper.
Lakukan ini hanya apabila anda sudah menulis 12 perkataan benih itu atau
dompet itu hanya dompet latihan. Kad itu sendiri tidak rosak dan anda boleh
flash semula kad itu seberapa banyak kali yang anda mahu.

![Nyahpasang memadam, pasang bermula dari awal](images/08-swap.svg)

1. Masuk ke folder Downloads anda, tempat fail applet berada:

   ```bash
   cd ~/Downloads
   ```

2. Tidak pasti apa yang ada pada kad? Jalankan `gp --list`: baris `APP`
   menunjukkannya.

3. Buang applet yang ada pada kad sekarang. Jalankan **hanya baris untuknya**:

   Satochip:

   ```bash
   gp --uninstall SatoChip-v0.15-0.1.cap
   ```

   Satodime:

   ```bash
   gp --uninstall Satodime-0.1-0.2.cap
   ```

   SeedKeeper:

   ```bash
   gp --uninstall SeedKeeper-v0.2-0.1.cap
   ```

   Keycard:

   ```bash
   gp --delete A0000008040001 -f
   ```

4. ✅ Jalankan `gp --list` sekali lagi. Hanya pengurus kad (`ISD`) dan pakej
   kilang (`A0000001515350`) yang sepatutnya tinggal.

5. Pasang applet baharu ([Langkah 4](#step-4)) dan sediakannya
   ([Langkah 5a](#step-5a), [5b](#step-5b) atau [5c](#step-5c)).

Contohnya:

![Menukar Satochip kepada Satodime](images/05-gp-swap-to-satodime.svg)

![Menukar Satodime kepada SeedKeeper](images/06-gp-swap-to-seedkeeper.svg)

## 🩺 Penyelesaian masalah

| Anda nampak | Apa yang perlu dibuat |
|---|---|
| 🍎 `brew: command not found` | Jalankan arahan **Next steps** yang dipaparkan oleh Homebrew selepas ia selesai dipasang. |
| 🐧 `gp: command not found` | Tutup Terminal dan buka yang baharu. |
| `No such file` atau `does not exist` | Jalankan `cd ~/Downloads` dahulu, dan semak fail itu ada dalam Downloads. |
| `Card cryptogram invalid` | Kad itu dikunci dengan kunci lain. Berhenti, jangan cuba lagi. |
| `LOAD failed: 0x6438` | Applet itu dibina untuk Java Card yang lebih baharu daripada yang ada pada kad. |
| `INSTALL ... failed: 0x6444` atau `0x6F00` | Kad itu tiada ciri yang diperlukan oleh applet. |
| `gp --install` gagal pada kad yang sudah ada applet | Jalankan `gp --list`. Jika ada baris `APP`, buang applet itu dahulu (lihat [Menukar kepada applet lain](#switching)). |
| **Scan...** dalam Sparrow tidak menjumpai apa-apa | Tutup Sato-Tools dan mana-mana aplikasi kad lain, keluarkan kad dan masukkan semula, kemudian tekan **Scan...** sekali lagi. |
| Terlupa PIN, atau kad disekat selepas terlalu banyak PIN yang salah | Buang applet itu dan pasang semula (ini memadam kad), kemudian pulihkan 12 perkataan benih anda. |
| Sato-Tools tidak nampak kad | Tutup dan buka semula Sato-Tools. |
| `gp` tidak menjumpai pembaca, atau menyatakan tiada kad dimasukkan | Masukkan kad, bahagian cip dahulu. Jika masih menyatakan tiada kad, pembaca itu tidak bertindak balas: cabut pembaca dari komputer, sambungkan semula dengan kad di dalamnya, dan cuba lagi. 🐧 Kemudian jalankan `sudo systemctl restart pcscd`. |

## 🚀 Lebih lanjut

- 🎥 [Video: flash 7 applet berbeza pada kad ini](https://www.youtube.com/watch?v=j13XrvPFZk0)
- 🔒 Kunci kad dengan kunci anda sendiri, supaya tiada orang lain boleh
  mengubah apa yang dipasang padanya:
  [Satochip-DIY: Locking Javacards](https://github.com/3rdIteration/Satochip-DIY#locking-javacards-optional)
- 🏗️ Bina sendiri applet itu. Fail di atas dibina oleh orang lain, jadi
  anda percaya bahawa fail itu sepadan dengan kod sumber awam. Membina
  sendiri daripada kod sumber bermakna apa yang masuk ke dalam kad anda
  ialah betul-betul kod yang boleh dibaca oleh sesiapa sahaja:
  [Satochip-DIY: Building](https://github.com/3rdIteration/Satochip-DIY#harder-but-better-method---building-on-your-own-pc)

## 🙏 Penghargaan

- 🙌 [Toporin](https://github.com/Toporin), pencipta
  [Satochip](https://satochip.io), untuk applet Satochip, Satodime dan
  SeedKeeper serta Sato-Tools.
- 🛠️ [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) oleh
  3rdIteration, panduan DIY yang diikuti oleh panduan ini, dengan senarai
  kad dan pembaca kad yang telah diuji serta `gp.jar`.
- [Keycard](https://github.com/keycard-tech/status-keycard) untuk applet
  Keycard.
- 💳 [GlobalPlatformPro](https://github.com/martinpaljak/GlobalPlatformPro)
  oleh Martin Paljak untuk memuatkan applet ke dalam kad.
