# 🖥️ Jalankan nod bitcoin anda sendiri

Nod anda sendiri menyemak setiap peraturan bitcoin dengan sendirinya, jadi
anda tidak perlu mempercayai pelayan orang lain. Panduan ini membantu anda
menyediakannya di rumah.

Panduan ini ialah versi Bahasa Melayu bagi ceramah tentang nod daripada
[Satoshi Spritz](https://satoshispritz.it/) di Itali (lihat
[Kredit](#credits)): mula-mula gambaran keseluruhan perisian dan
perkakasan nod, kemudian binaan langkah demi langkah pada ODROID-M1
dengan Bitcoin Core, Tor dan electrs.

## 🤔 Kenapa jalankan nod bitcoin?

- Semak sendiri seluruh blockchain bitcoin
- Semak baki dompet anda tanpa pihak ketiga
- Siarkan transaksi anda sendiri
- Privasi paling tinggi, tiada kepercayaan pada perkhidmatan luar

## 📦 Perisian nod

Pilihan siap pakai:

| Perisian | Harga | Ciri |
|---|---|---|
| Umbrel | Perisian percuma, perkakasan 100 hingga 300 € | Paling mudah digunakan, pasaran aplikasi, gaya Apple |
| MyNode | Percuma / 79 € premium / 399 hingga 599 € siap pasang | Nod + Lightning + Electrum + penjelajah blok |
| Start9 | 699 hingga 899 € siap guna / perisian percuma untuk DIY | Sumber terbuka, Tor terbina dalam, tiada telemetri |

Keperluan biasa:

- Blockchain: kira-kira 670+ GB (2025), bertambah kira-kira 1 GB setiap
  beberapa hari
- Sekurang-kurangnya 4 GB RAM, SSD 1 TB (2 TB disyorkan)
- Penyegerakan pertama: 2 hingga 7 hari

**Siap pakai:** cepat dipasang dan ada antara muka grafik, tetapi Docker,
skrip dan semakan keselamatan tersembunyi daripada anda, dan perisian
tambahan sukar dipantau.

**DIY (manual):** kurang perisian bermakna kurang permukaan serangan,
kawalan penuh atas setiap bahagian, dan anda belajar menjaganya.

## 🔧 Memilih perkakasan

Minimum:

- Sekurang-kurangnya 2 GB RAM (4 GB disyorkan)
- Cakera sekurang-kurangnya 1 TB (2 TB disyorkan)
- CPU berbilang teras (mana-mana CPU baharu)

Pilihan:

- **PC lama:** tiada kos; keluarkan bateri jika ia komputer riba
- **Raspberry Pi:** mahal, dan cakera USB kurang stabil
- **ODROID-M1:** ARM, dengan slot dalaman untuk SSD
- **Thin client (30 hingga 100 €):** Fujitsu Futro S920, HP t620

Untuk sistem pengendalian, gunakan **Debian 13**, Linux sokongan jangka
panjang dengan kemas kini bertahun-tahun. Kemas kini semua pakej, pasang
`tor` dan `ssh` untuk menjaga nod dari jauh, dan UPS (bateri sandaran)
disyorkan.

# 🛠️ Bina nod pada ODROID-M1

## 🧰 Apa yang anda perlukan

- ODROID-M1
- SSD NVMe (2 TB paling baik)
- Penghala
- Kabel Ethernet
- Monitor dengan kabel HDMI
- Tetikus dan papan kekunci USB

## 🔩 Langkah 1: pasang

1. Pasang SSD NVMe ke dalam ODROID-M1.
2. Sambungkan semua kabel: tetikus dan papan kekunci, monitor, kabel
   Ethernet.
3. Sambungkan kabel kuasa untuk menghidupkan ODROID-M1.

## 🐧 Langkah 2: muat turun Debian 12

1. Selepas ODROID-M1 hidup, pergi ke **Exit to shell** dalam Petitboot
   dan tekan **Enter**.
2. Taip `udhcpc` dan tekan **Enter** (ini boleh membantu membetulkan
   masalah sambungan).
3. Taip `netboot_default` dan tekan **Enter**.

   ⌨️ Papan kekunci ialah susun atur Amerika: untuk `_` tahan **Shift**
   dan tekan kekunci di sebelah **0**.

4. Taip `exit` dan tekan **Enter**.
5. Skrin utama Petitboot kini menunjukkan senarai imej yang ada. Pergi ke
   **Debian 12 (Bookworm) Netboot installer** dan tekan **Enter**.

## 💿 Langkah 3: pasang Debian 12

1. **Bahasa:** pilih **English**, lebih mudah diikuti (terjemahannya
   mungkin tidak bagus).
2. **Lokasi:** pilih negara anda (untuk Itali: **other**, kemudian
   **Europe**, kemudian **Italy**).
3. **Tempatan:** anda boleh kekalkan bahasa Inggeris dengan memilih
   **United Kingdom**.
4. **Papan kekunci:** pilih papan kekunci yang anda ada (untuk papan
   kekunci QWERTY Itali, **Italian**).
5. **Hostname:** pilih nama hos. Domain boleh dibiarkan kosong.
6. **Negara cermin:** negara tempat Debian dimuat turun (di Itali,
   **Italy**).
7. **Cermin:** di Itali, `debian.mirror.garr.it` biasanya laju dan
   boleh dipercayai.
8. **Proksi:** jika tiada proksi, biarkan kosong dan tekan **Enter**.
9. **Kata laluan root:** tetapkan kata laluan superuser dan tekan
   **Enter**.
10. **Pengguna:** reka nama untuk pengguna biasa (bukan admin) (tidak
    perlu isi nama penuh), kemudian tetapkan kata laluannya.
11. **Cakera:** pilih **Guided: use entire disk**, kemudian cakera NVMe
    yang anda pasang.
12. **Partisi:** simpan semuanya dalam satu partisi: **All files in one
    partition (recommended for new users)**.
13. Debian membuat jadual dengan partisi boot, partisi root dan partisi
    swap, yang kita tidak mahu. Pergi ke baris swap, tekan **Enter**,
    pilih **Delete the partition** dan tekan **Enter**.
14. Pilih **Finish partitioning and write changes to disk**.
15. Debian mengingatkan bahawa tiada partisi swap. Pilih **No** supaya
    anda tidak kembali ke menu partisi.
16. Sahkan dua partisi (boot dan root) dengan **Yes** untuk mula
    memasang.
17. **Popularity contest** (statistik tanpa nama tentang pakej Debian
    yang anda guna): pilih **No**.
18. **Perisian:** kita tidak mahu desktop grafik, jadi tandakan hanya
    **SSH server** dan **standard system utilities**, kemudian tekan
    **Enter**.
19. **Pemasangan selesai:** pilih **Continue** untuk mula semula.

## 🔁 Langkah 4: mulakan Debian secara automatik

1. Dalam Petitboot, pergi ke **System configuration** dan tekan
   **Enter**.
2. **Autoboot** mesti **Enabled**.
3. **Boot order** mesti mengandungi cakera yang dipasang dalam ODROID-M1,
   atau **Any device**. Untuk menambah cakera, pergi ke **Add Device**,
   pilih cakera dan **OK**. Atau pilih **Clear & Boot Any** untuk **Any
   device**.
4. **Timeout** ialah bilangan saat selepas hidup sebelum Debian bermula
   (10 secara lalai, tidak perlu diubah).
5. Pergi ke **OK** dan tekan **Enter**, kemudian mulakan semula
   ODROID-M1. Selepas itu kiraan detik di bawah kiri memulakan Debian 12.

<details>
<summary><b>📵 Tiada sambungan internet?</b></summary>

1. Log masuk sebagai superuser `root` dan jalankan `ping 8.8.8.8`. Jika
   ia berkata **Network is unreachable**, peranti tidak bersambung.
2. Jalankan `dhclient -v` untuk meminta alamat IP daripada penghala
   melalui DHCP. Ini juga menunjukkan nama antara muka rangkaian,
   contohnya `eth0`.
3. Jalankan `ping 8.8.8.8` sekali lagi: kini ia berfungsi, tetapi
   masalahnya kembali selepas mula semula seterusnya.
4. Supaya `eth0` menggunakan DHCP setiap kali bermula, jalankan
   `nano /etc/network/interfaces` dan tambah atau ubah baris ini:

   ```
   auto eth0
   iface eth0 inet dhcp
   ```

   Tekan **Ctrl + X** untuk keluar, **y** untuk simpan dan **Enter**
   untuk tulis ganti.

5. Mulakan semula dan semak sambungan sekali lagi. Kini ODROID-M1
   bersambung sendiri setiap kali dimulakan semula.

</details>

## 💻 Langkah 5: sambung dengan SSH

1. Pada komputer anda, buka terminal dan sambung dengan SSH, menggunakan
   pengguna yang anda buat semasa pemasangan dan alamat IP yang diberi
   oleh penghala kepada ODROID-M1:

   ```bash
   ssh USERNAME@IP_ADDRESS
   ```

   Debian tidak membenarkan log masuk melalui SSH sebagai superuser
   (`root`).

2. Selepas log masuk, taip `su` dan masukkan kata laluan superuser untuk
   mendapat haknya.

## 📥 Langkah 6: kemas kini repositori dan sistem

1. Buka senarai repositori:

   ```bash
   nano /etc/apt/sources.list
   ```

2. Padam semua baris dan tambah yang ini:

   ```
   deb http://deb.debian.org/debian/ bookworm contrib main non-free non-free-firmware
   # deb-src http://deb.debian.org/debian/ bookworm contrib main non-free non-free-firmware

   deb http://deb.debian.org/debian/ bookworm-updates contrib main non-free non-free-firmware
   # deb-src http://deb.debian.org/debian/ bookworm-updates contrib main non-free non-free-firmware

   deb http://deb.debian.org/debian/ bookworm-proposed-updates contrib main non-free non-free-firmware
   # deb-src http://deb.debian.org/debian/ bookworm-proposed-updates contrib main non-free non-free-firmware

   deb http://deb.debian.org/debian/ bookworm-backports contrib main non-free non-free-firmware
   # deb-src http://deb.debian.org/debian/ bookworm-backports contrib main non-free non-free-firmware

   deb http://deb.debian.org/debian-security/ bookworm-security contrib main non-free non-free-firmware
   # deb-src http://deb.debian.org/debian-security/ bookworm-security contrib main non-free non-free-firmware
   ```

   Tekan **Ctrl + X**, kemudian **y** untuk simpan dan **Enter** untuk
   tulis ganti fail.

3. Kemas kini senarai pakej dan pakej yang dipasang:

   ```bash
   apt update
   ```

   ```bash
   apt upgrade
   ```

   ✅ `apt update` kini sepatutnya bersambung ke repositori di atas,
   bukan ke `debian.mirror.garr.it/debian` yang lalai.

## 🧅 Langkah 7: pasang Tor dan capai SSH melalui Tor

1. Pasang Tor:

   ```bash
   apt install tor
   ```

2. Buka tetapannya dengan `nano /etc/tor/torrc` dan tambah di bahagian
   bawah:

   ```
   HiddenServiceDir /var/lib/tor/hidden_service/
   HiddenServicePort 22 127.0.0.1:22
   ```

   Tekan **Ctrl + X**, **y** dan **Enter**.

3. Mulakan semula Tor dan semak statusnya (ia bermula setiap kali
   ODROID-M1 bermula):

   ```bash
   systemctl restart tor
   ```

   ```bash
   systemctl status tor
   ```

4. Tunjukkan alamat onion anda:

   ```bash
   cat /var/lib/tor/hidden_service/hostname
   ```

5. Kini anda boleh sambung ke ODROID-M1 dari mana-mana di dunia:

   ```bash
   torify ssh USERNAME@YOUR_ADDRESS.onion
   ```

## 🟠 Langkah 8: muat turun Bitcoin Core

Versi terbaharu Bitcoin Core, hash dan tandatangannya ada di
<https://bitcoincore.org/en/download/>.

1. Muat turun pakej pemasangan, checksum dan tandatangan:

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS.asc
   ```

2. Semak checksum fail yang dimuat turun sama dengan yang dalam
   `SHA256SUMS`:

   ```bash
   sha256sum --ignore-missing --check SHA256SUMS
   ```

   ✅ Ia mesti menunjukkan **OK** selepas nama fail:
   `bitcoin-28.0-aarch64-linux-gnu.tar.gz: OK`

3. Kunci awam pembangun Bitcoin Core ada dalam repositori
   `bitcoin-core/guix.sigs`. Anda mungkin belum ada git, jadi pasang
   dahulu:

   ```bash
   apt-get install git
   ```

   ```bash
   git clone https://github.com/bitcoin-core/guix.sigs
   ```

4. Import kunci awam pembangun:

   ```bash
   gpg --import guix.sigs/builder-keys/*
   ```

5. Semak tandatangan pada `SHA256SUMS.asc`:

   ```bash
   gpg --verify SHA256SUMS.asc
   ```

   ✅ Untuk setiap pembangun anda sepatutnya nampak
   `gpg: Good signature from "...."`, yang bermakna tandatangan itu sah.
   Anda boleh abaikan sebarang amaran.

🔒 Kenapa ini penting:

- **Integriti:** checksum memastikan fail tidak rosak atau diubah semasa
  dimuat turun.
- **Ketulenan:** tandatangan mengesahkan fail benar-benar daripada
  pembangun Bitcoin Core dan bukan daripada sumber berniat jahat.

## ⚙️ Langkah 9: pasang Bitcoin Core dan mula menyegerak

1. Buka pakej:

   ```bash
   tar xzvf bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

2. Pasang Bitcoin Core:

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin bitcoin-28.0/bin/*
   ```

3. Mula menyegerak:

   ```bash
   bitcoind
   ```

4. Tetapan untuk rumah: buka `bitcoin.conf` dengan
   `nano ~/.bitcoin/bitcoin.conf` dan tambah:

   ```ini
   daemon=1
   blocksonly=1
   maxconnections=20
   maxuploadtarget=500
   txindex=1
   blockfilterindex=1
   ```

   Tekan **Ctrl + X**, **y** dan **Enter**.

| Tetapan | Apa yang ia buat |
|---|---|
| `daemon=1` | Menjalankan `bitcoind` sebagai daemon di latar belakang, jadi ia terus berjalan selepas anda tutup terminal |
| `blocksonly=1` | Muat turun blok sahaja, bukan mempool (transaksi belum disahkan) |
| `maxconnections=20` | Paling banyak 20 sambungan masuk dan keluar |
| `maxuploadtarget=500` | Muat naik paling banyak 500 MB sehari |
| `txindex=1` | Menyimpan indeks penuh transaksi (jelajah seluruh blockchain) |
| `blockfilterindex=1` | Membina dan menyimpan indeks penapis blok (transaksi boleh disemak dengan lebih cekap) |

💡 Sejak Bitcoin Core 26, `v2transport` menyulitkan sambungan antara nod.
Untuk semua tetapan, lihat
[penjana konfigurasi Bitcoin Core](https://jlopp.github.io/bitcoin-core-config-generator/).

<details>
<summary><b>🔁 Jalankan sebagai perkhidmatan systemd</b></summary>

Dengan perkhidmatan `bitcoind`, pengguna khas `bitcoin`, mula semula
automatik dan log dalam `~/.bitcoin/debug.log`:

```bash
sudo systemctl enable bitcoind
```

```bash
sudo systemctl start bitcoind
```

```bash
systemctl status bitcoind
```

</details>

## 🧅 Langkah 10: Bitcoin Core melalui Tor sahaja

Tetapkan Bitcoin Core supaya bersambung, dan boleh dicapai, melalui Tor
sahaja.

1. Buka tetapan Tor dengan `nano /etc/tor/torrc` dan tambah di hujung:

   ```
   HiddenServiceDir /var/lib/tor/bitcoin-service/
   HiddenServicePort 8333 127.0.0.1:8333
   ```

   Tekan **Ctrl + X**, **y** dan **Enter**.

2. Mulakan semula Tor:

   ```bash
   systemctl restart tor
   ```

3. Tunjukkan alamat onion Bitcoin Core:

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. Buka `bitcoin.conf` dengan `nano ~/.bitcoin/bitcoin.conf` dan tambah:

   ```ini
   proxy=127.0.0.1:9050
   listen=1
   bind=127.0.0.1:8333=onion
   externalip=ADDRESS.onion
   onlynet=onion
   ```

   Tekan **Ctrl + X**, **y** dan **Enter**.

| Tetapan | Apa yang ia buat |
|---|---|
| `proxy=127.0.0.1:9050` | Membuat Bitcoin Core menggunakan proksi Tor |
| `listen=1` | Membenarkan nod menerima sambungan masuk |
| `bind=127.0.0.1:8333=onion` | Membenarkan sambungan masuk melalui Tor sahaja |
| `externalip=ADDRESS.onion` | Gantikan `ADDRESS.onion` dengan alamat onion yang anda dapat tadi |
| `onlynet=onion` | Membenarkan sambungan keluar pada rangkaian Tor sahaja |

5. Mulakan semula Bitcoin Core: hentikannya, kemudian mulakan semula
   sebagai daemon (proses di latar belakang):

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

6. Semak rangkaian:

   ```bash
   bitcoin-cli getnetworkinfo
   ```

   ✅ Di bawah `networks` anda nampak `ipv4`, `ipv6`, `onion`, `i2p` dan
   `cjdns`. Hanya `onion` yang `reachable` bernilai `true`; semua yang
   lain `false`. Di bawah `localaddresses` anda nampak alamat onion anda
   sahaja.

7. Semak peer yang anda sambung, atau yang sambung kepada anda:

   ```bash
   bitcoin-cli getpeerinfo
   ```

   ✅ Medan `addr` mengandungi alamat onion peer dan `network` berkata
   `onion`.

⏳ Tor perlahan: penyegerakan pertama mengambil masa lebih lama.

<details>
<summary><b>🔄 Tukar alamat onion Bitcoin Core</b></summary>

1. Padam folder `bitcoin-service`, yang menyimpan kunci peribadi di
   sebalik alamat onion:

   ```bash
   rm -r /var/lib/tor/bitcoin-service/
   ```

2. Mulakan semula Tor, yang membuat folder itu semula dengan kunci awam
   dan peribadi baharu:

   ```bash
   systemctl restart tor
   ```

3. Semak alamat onion baharu:

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. Mulakan semula Bitcoin Core:

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

</details>

## 🦀 Langkah 11: pasang Rust

electrs ialah pelayan Electrum yang ditulis dalam bahasa pengaturcaraan
Rust, jadi anda perlukan Rust pada peranti.

1. Pasang `curl` dahulu:

   ```bash
   apt install curl
   ```

2. Pasang Rust:

   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   ```

   Apabila ia meminta pilihan pemasangan, taip **1** dan tekan **Enter**
   untuk pemasangan lalai.

3. Apabila selesai ia mencadangkan anda mulakan semula shell. Log keluar
   dan masuk semula (atau jadi superuser semula dengan `su`) untuk
   menggunakan arahan Rust.

4. Pasang `clang`, `cmake` dan `build-essential`:

   ```bash
   apt install clang cmake build-essential -y
   ```

## 🔎 Langkah 12: muat turun electrs

1. Semak versi terbaharu electrs di
   <https://github.com/romanz/electrs>. Di sini ia 0.10.7:

   ```bash
   VERSION="0.10.7"
   ```

   (Jika versi terbaharu ialah, katakan, 0.10.9, ubah nilai dalam tanda
   petik.)

2. Muat turun versi itu:

   ```bash
   git clone --branch v$VERSION https://github.com/romanz/electrs.git
   ```

3. Masuk ke folder `electrs` yang baharu dan import kunci pembangun:

   ```bash
   cd electrs
   ```

   ```bash
   curl https://romanzey.de/pgp.txt | gpg --import
   ```

4. Semak tandatangan pembangun:

   ```bash
   git verify-tag v$VERSION
   ```

   ✅ Tandatangan sah jika anda nampak `gpg: Good signature from "...."`.

## 🏗️ Langkah 13: bina dan pasang electrs

1. Masih dalam folder `electrs`, mulakan binaan. Ia mengambil masa yang
   agak lama, jadi bersabarlah 😄

   ```bash
   cargo build --locked --release
   ```

2. Pasang electrs:

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin ./target/release/electrs
   ```

3. Semak ia dipasang dengan memaparkan versinya:

   ```bash
   electrs --version
   ```

## 📝 Langkah 14: tetapan bitcoin.conf untuk electrs

1. Hentikan Bitcoin Core:

   ```bash
   bitcoin-cli stop
   ```

2. Buka `bitcoin.conf` dengan `nano ~/.bitcoin/bitcoin.conf` dan tambah:

   ```ini
   server=1
   rpcuser=USER
   rpcpassword=PASSWORD
   rpcallowip=127.0.0.1
   whitelist=download@127.0.0.1
   ```

   Tekan **Ctrl + X**, **y** dan **Enter**.

| Tetapan | Apa yang ia buat |
|---|---|
| `server=1` | Membuat Bitcoin Core berfungsi sebagai pelayan RPC |
| `rpcuser=USER` | Nama pengguna (anda pilih) yang mesti diberi aplikasi untuk sambung ke nod melalui RPC |
| `rpcpassword=PASSWORD` | Kata laluan (anda pilih) untuk nama pengguna itu |
| `rpcallowip=127.0.0.1` | Alamat IP yang boleh guna RPC nod: di sini hanya program pada mesin yang sama |
| `whitelist=download@127.0.0.1` | Program pada mesin yang sama boleh muat turun blok dan transaksi tanpa had, walaupun peraturan lain akan menghalangnya (contohnya `maxuploadtarget=500`). Ringkasnya, electrs mendapat akses bebas kepada data nod |

3. Mulakan Bitcoin Core semula:

   ```bash
   bitcoind -daemon
   ```

## 📝 Langkah 15: config.toml electrs

1. Dalam folder tempat anda memasang electrs, buat fail tetapan:

   ```bash
   nano config.toml
   ```

2. Tambah baris ini:

   ```toml
   # bitcoin core configuration
   auth = "USER:PASSWORD"
   daemon_rpc_addr = "127.0.0.1:8332"
   daemon_p2p_addr = "127.0.0.1:8333"

   # electrs configuration
   db_dir = ".electrum"
   network = "bitcoin"
   electrum_rpc_addr = "127.0.0.1:50001"
   log_filters = "INFO"
   ```

   Tekan **Ctrl + X**, **y** dan **Enter**.

| Tetapan | Apa yang ia buat |
|---|---|
| `auth = "USER:PASSWORD"` | Gantikan `USER` dan `PASSWORD` dengan yang sama seperti dalam `bitcoin.conf`, untuk log masuk melalui RPC |
| `daemon_rpc_addr = "127.0.0.1:8332"` | Alamat dan port RPC nod: `127.0.0.1` bermaksud mesin yang sama, `8332` ialah port RPC lalai Bitcoin Core |
| `daemon_p2p_addr = "127.0.0.1:8333"` | Alamat dan port P2P nod: `8333` ialah port P2P lalai Bitcoin Core |
| `db_dir = ".electrum"` | Folder tempat electrs menyimpan pangkalan datanya |
| `network = "bitcoin"` | Rangkaian tempat electrs berjalan: di sini rangkaian utama bitcoin |
| `electrum_rpc_addr = "127.0.0.1:50001"` | Tempat electrs menawarkan perkhidmatannya kepada klien Electrum: `127.0.0.1` bermaksud dari mesin yang sama sahaja, `50001` ialah port lalai untuk sambungan Electrum tanpa penyulitan |
| `log_filters = "INFO"` | Berapa banyak butiran yang ditulis electrs dalam lognya |

## ⏳ Langkah 16: bina indeks electrs

1. Dalam folder electrs, mulakan pengindeksan blok:

   ```bash
   electrs --conf config.toml
   ```

2. Biarkan terminal terbuka sehingga seluruh rangkaian diindeks. Ia
   boleh mengambil masa berjam-jam: dalam ceramah itu ia bermula pada 5
   Disember jam 6:21 dan selesai pada 6 Disember jam 1:24, selepas 19 jam
   3 minit.

## 🧅 Langkah 17: electrs melalui Tor

1. Buka tetapan Tor dengan `nano /etc/tor/torrc` dan tambah di hujung:

   ```
   HiddenServiceDir /var/lib/tor/electrs_hidden_service/
   HiddenServiceVersion 3
   HiddenServicePort 50001 127.0.0.1:50001
   ```

   Tekan **Ctrl + X**, **y** dan **Enter**.

2. Mulakan semula Tor:

   ```bash
   systemctl restart tor
   ```

3. Tunjukkan alamat onion electrs:

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

<details>
<summary><b>🔄 Tukar alamat onion electrs</b></summary>

1. Padam folder `electrs_hidden_service`, yang menyimpan kunci peribadi
   di sebalik alamat onion:

   ```bash
   rm -r /var/lib/tor/electrs_hidden_service/
   ```

2. Mulakan semula Tor, yang membuat folder itu semula dengan kunci awam
   dan peribadi baharu:

   ```bash
   systemctl restart tor
   ```

3. Semak alamat onion baharu:

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

</details>

# 📚 Lagi

## ➕ Langkah seterusnya

- **Core Lightning (CLN):** Lightning Network untuk pembayaran, disediakan
  dalam pelajaran berasingan.
- **Mempool.space:** penjelajah blok yang dihos sendiri, menggunakan
  MariaDB, Node.js, backend dan frontend, dengan `pm2` untuk mengurus
  proses.

## 🧪 Testnet

Untuk belajar: syiling tanpa nilai dan yuran sangat kecil. Perlombongan
naik turun dan kesukaran berubah.

- Dompet: Green, Electrum, Specter, Sparrow
- BTCBouncer: pembayaran testnet
- BTCSigner: multisig testnet

## ⌨️ bitcoin-cli

Format: `bitcoin-cli [options] <command> [params]`, dan `bitcoin-cli
help` menyenaraikan semuanya.

- Blockchain: `getblockcount`, `getblockchaininfo`, `getblock`
- Rangkaian: `getnetworkinfo`, `getpeerinfo`
- Dompet: `getbalance`, `sendtoaddress`, `listunspent`
- Raw: `createpsbt`, `decodepsbt`, `signrawtransactionwithwallet`

## 🔗 Sumber

- [Pelajaran Officine Bitcoin](https://officinebitcoin.it/lezioni/fulhar/):
  perkakasan, Bitcoin Core, electrs, testnet, bitcoin-cli
- [Pelajaran Debian Officine Bitcoin](https://officinebitcoin.it/lezioni/debian/)
- [Learning Bitcoin from the Command Line](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line),
  termasuk [Using Tor](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_0_Using_Tor.md)
  dan [Changing Your Bitcoin Hidden Services](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_2_Changing_Your_Bitcoin_Hidden_Services.md)
- [Bitcoin Core](https://bitcoincore.org/) dan
  [nota Tornya](https://github.com/bitcoin/bitcoin/blob/master/doc/tor.md)
- [Setting up a Tor hidden service](https://en.bitcoin.it/wiki/Setting_up_a_Tor_hidden_service)
- [electrs](https://github.com/romanz/electrs) dan
  [dokumen konfigurasinya](https://github.com/romanz/electrs/blob/master/doc/config.md)
- [Penjana konfigurasi Bitcoin Core](https://jlopp.github.io/bitcoin-core-config-generator/)
- [Mempool](https://mempool.space/)
- [Autoboot Petitboot ODROID-M1](https://wiki.odroid.com/getting_started/petitboot/os_installation_using_otg?s[]=autoboot#set_autoboot)
  dan [bebenang forum ODROID](https://forum.odroid.com/viewtopic.php?t=37109)

## 🧱 Panduan lain

- [MiniBolt](https://minibolt.minibolt.info/): bina nod pada PC mini
  dengan pemproses Intel atau AMD dari sekitar tahun 2010 dan seterusnya.

💬 Ditemui dalam sembang Telegram Malaga 2140.

[Keperluan perkakasan](https://github.com/minibolt-guide/minibolt/blob/main/index-1/preparations.md#hardware-requirements) MiniBolt sendiri
menyenaraikan:

- CPU: pemproses Intel/AMD (generasi 2010 ke atas)
- RAM: 2 GB RAM atau lebih (Bitcoin sahaja), 4 GB RAM atau lebih disyorkan
  (Bitcoin, Lightning dan tambahan)
- Storan: SSD SATA/NVMe 2 TB atau lebih disyorkan
- Pemacu pen USB atau kad SD 4 GB atau lebih untuk memasang
- Monitor atau TV sementara dan papan kekunci USB atau PS2 untuk persediaan
  pertama

<a id="credits"></a>

## 🙏 Kredit

Diterjemah daripada ceramah [Satoshi Spritz](https://satoshispritz.it/)
ini (dalam bahasa Itali):

- [Nod bitcoin: perisian, perkakasan dan DIY](https://satoshispritz.it/presentazioni/260311-bitcoin-node.pdf),
  Valerio Vaccaro, Satoshi Spritz Milano, lesen
  [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)
- [Nod bitcoin pada ODROID-M1, bahagian 1: memasang sistem pengendalian](https://satoshispritz.it/presentazioni/241031-odroid.pdf),
  Satoshi Spritz Treviso, 29 Oktober 2024
- [Nod bitcoin pada ODROID-M1, bahagian 2: memasang Bitcoin Core dan mula menyegerak](https://satoshispritz.it/presentazioni/241031-odroid2.pdf),
  Satoshi Spritz Treviso, 19 November 2024
- [Nod bitcoin pada rangkaian Tor](https://satoshispritz.it/presentazioni/241031-tor.pdf),
  Satoshi Spritz Treviso, 14 Januari 2025
- [Pelayan Electrum: electrs](https://satoshispritz.it/presentazioni/241031-electrs.pdf),
  Satoshi Spritz Treviso, 11 Februari 2025
